######################################
Application Architecture (Server side)
######################################

Architectural Pattern
+++++++++++++++++++++
Supermart application on the serverside is designed as a monolithic applicaiton 
seperated into modules that encapsulate distinct parts/functions of the application.
Two endpoints are surfaced to access the application with the remaining modules linked into 
these endpoints as libraries. These endpoints are the admin and the Frontend interfaces. 

The reason for choosing this style as opposed to microservices is that at the moment the application 
is relatively small (code base wise ) and does not have high traffic volumes to introduce scaling hurdles, 
that would warrant seperating it into micro services. Microservices introduce latencies while connecting to each other 
as opposed to method calls within the same process. 

The admin and the frontend are also seperated (deployed seperately) in order to make it possible to deploy the 
admin behind a firewall or add any other access restrictions in case necessary. 

Further the admin and fronted modules only provide API access to the remainder of the application. The remainder of the modules
are conceptually divided into three logical parts
   #. Model - this is the domain object or persistence entity
   #. Data access layer - this provides the actual persistence to the Data store of the models
   #. Service layer - This layer provides: 
   
         #. Transaction Managaement
         #. Access to the Data Access Layer
         #. Business logic
 

Modules
+++++++

Core
----
 This module contains a lot of the shared functionalites for the application as well as the customer related business logic


Coupons
-------
This module contains logic related to coupons and their usage across the system

Orders
------
This contains logic related to orders, cart and the entire order process

Oms
---
This module contains logic for OMS related activites

Products
--------
This module provides logic for product catalogue and inventory for the products


Recipes
-------
This module contains business logic related to recipes


Admin
-----
This module provides API access for admin functionalities, admin UI and scheduled tasks

Frontend
--------
This module provides API access for frontend functionalities and frontend UI



Caching
+++++++
To optimize performance content for certain pages rendered to the client is cached. Most static content served to the client
is set to expire after 365 days. The static content contains a file checksum or a timestamp in its name to distinguish it from previous versions
and prevent usage of old outdated content. 

Secondly on the server side some of the APIs also cache their responses since they are mostly always the same.  


Environments
++++++++++++
Currently the system supports three environment configurations : inhouse,production and local. These configurations 
are used to affect application behaviour either during build phase or at runtime.

During the build phase the configurations are used to affect

   #. path to node, bower and gulp on given env
   #. select Facebook,google api keys to use for given environment
   #. Payment gateway configurations for the given environment
   #. CDN path
   #. Mailchimp configurations

During runtime environment configurations are used primarily to :
   #. Determine whether to run certain scheduled tasks
   #. Where to send out emails to... WE cannot have emails going to customers from test boxes 
      and we still need to see the emails going out. 


Build Process
+++++++++++++
The build process is controlled entirely by maven using the maven lifecycle.
   
   #. generate sources - some classes for webservices (to connect to migs) have to be generated
   #. compilation - this performs compilation of java source files
   #. prepare package - at this stage we:
     - compile sass files into css
     - Install required node modules
     - run bower to install dependencies
     - run gulp to aggregate js, css and templates and then minify them
     - run java program to add a checksum to the file names to prevent caching
       and create include stumps for JS and css files
     - copy necessary index file to home folder depending on whether we want compiled & minified 
       files or raw files. This is dependend on environment configuration
       
       
    


