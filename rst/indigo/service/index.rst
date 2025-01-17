Indigo Service
==============

.. toctree::
    :hidden:
    :includehidden:

    index


Summary
-------

Indigo service is an application for web usage. Indigo service provides RESTful API for the following operations:

- Aromatize, dearomatize molecules and reactions
- 2D Layout for structures
- Converting formats in all directions (Molfile,Smiles,Inchi,IUPAC name,CML,etc.)
- Rendering structures from text format into images (PNG,PDF)
- Image recognition toolkit
- Calculating molecular and reaction properties



API
---

Indigo service provides REST API 

.. list-table::
   :header-rows: 1
   :stub-columns: 1
   :widths: 30 70

   * - Endpoint
     - Description
   * - /v2/indigo/aromatize
     - Aromatize input  structure
   * - /v2/indigo/calculate
     - Calculate properites for input structure
   * - /v2/indigo/check
     - Verifies input structure according to given types to Molfile/Rxnfile, SMILES, CML or InChI
   * - /v2/indigo/clean
     - Clean up structure or selected substructure coordinates
   * - /v2/indigo/convert
     - Convert structure to Molfile/Rxnfile, SMILES, CML or InChI 
   * - /v2/indigo/dearomatize
     - Dearomatize structure
   * - /v2/indigo/info
     - Return version for the indigo library
   * - /v2/indigo/render
     - Render structure to PNG, SVG or PDF





Installation
------------

Indigo service can be installed as a Docker image. Please see Downloads() section to find a latest version

1. Install `Docker <https://www.docker.com/>`__
2. Download image with indigo service from `Downloads <../../download/indigo.html>`__ 
3. Add the following configuration for nginx server

.. code-block:: sh
  
   location / {
       root /srv/www;
       index index.html;
       try_files $uri $uri/ @indigoservice;
   }
   location @indigoservice {
       # Should be set 'always' to transfer our lovely HTTP500 errors
       # see: https://epa.ms/d6u2d
       # Headers could be also set by Flasgger in service/config.py
       add_header 'Access-Control-Allow-Origin' '*' always;
       add_header 'Access-Control-Allow-Methods' 'POST, GET, PUT, DELETE, OPTIONS' always;
       add_header 'Access-Control-Allow-Headers' 'Accept, Content-Type' always;
       add_header 'Access-Control-Max-Age' '86400' always;
       include uwsgi_params;
       uwsgi_pass ${indigo_host}:8002;
   }


Run from Docker Hub
===================

Run Docker container by the following command

.. code-block:: sh

    docker run --restart=always -d -p 8002:8002 -e "INDIGO_UWSGI_RUN_PARAMETERS=--plugin python3 --py-autoreload=1" 
    -e "PYTHONPATH=/srv/indigo-python" -e "PYTHONDONTWRITEBYTECODE=1" --name=indigo_service  
    epmlsop/indigo_service:latest /bin/sh -c "supervisord -n"


Run from Local Image
====================
    
1. Import docker image



.. code-block:: sh

    cat indigo_service_${version}.tar.gz | docker import -  indigo_service:${version}


2. Run Docker container by the following command


.. code-block:: sh

    docker run --restart=always -d -p 8002:8002 -e "INDIGO_UWSGI_RUN_PARAMETERS=--plugin python3 --py-autoreload=1" 
    -e "PYTHONPATH=/srv/indigo-python" -e "PYTHONDONTWRITEBYTECODE=1" --name=indigo_service_${version}  
    indigo_service:${version} /bin/sh -c "supervisord -n"



