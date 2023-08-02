# Using Google Cloud Logging to write application logs
#### Using Google Cloud resources to add Cloud logging to an app 

### Logging events to Google Cloud


Writing  logs to Google Cloud Logging services can be done  in two ways:
1. Using the Python logging handler included with the Logging client library
   1. [Connecting the Google Cloud Logging library to Python logging](https://cloud.google.com/logging/docs/setup/python#connecting_the_library_to_python_logging)
   2. [Using the Python root logger](https://cloud.google.com/logging/docs/setup/python#using_the_python_root_logger)
2. Using [Cloud Logging API Cloud client library](https://cloud.google.com/logging/docs/setup/python#use_the_cloud_client_library_directly) for Python directly.


## Recommended practice for writing app logs
* Google recommends integrating Google Cloud Logging with the standard python logging library for [writing app logs](https://cloud.google.com/appengine/docs/standard/python3/writing-application-logs#writing_app_logs).
  * Allows reusing code modules with standard python logging methods.
  * Once the Google Cloud Logging is set up to use the Python root logger, no specific code is needed to log to Google Cloud
* For apps whose specific purpose is related to Google Cloud Logging, such as application logs analytics or logging configuration, the [Google Cloud Logging API](https://cloud.google.com/logging/docs/reference/libraries) provides advanced logs manipulation methods.

## Cloud logging implementation

This demo application  uses a Google Cloud Logging handler integrated with standard python logging and uses methods form python standard logging module by 
implementing a class called GoogleCloudLogManager

**GoogleCloudLogManager**
1. Creates a Google Cloud Logging client from a service account key file  
 *Note: required IAM roles for service account: Logs Writer*
3. Creates a Google Cloud Log handler that writes logs to a Google Cloud log with a specific name defined in the Flask app configuration.
4. Integrates the handler with the standard python Logging class, allowing the use of standard python logging methods whilst using Google Cloud as the logs store.
```console
     import logging
     logging.info(info_msg)
     logging.warning(warn_msg)
     logging.error(error_data) 
```

4. Also, for illustrative purposes, the class GoogleCloudManager:
* Creates a google.cloud.logging.Logger to use the Cloud Logging API directly if so desired
```console
     import google.cloud.logging 
     cloud_logger = google.cloud.logging.Logger()      
     cloud_logger.log_text(info_msg, severity='INFO')
     cloud_logger.log_text(warn_msg, severity='WARNING')
     cloud_logger.log_struct(error_data, severity='ERROR')
```
* Defines a log_example method to illustrate both ways of leveraging Google Cloud 


5. Class use example to add logging to an app
```console
    # App specific
    # Link Google Cloud Manager to app
    gl = GoogleCloudLogManager()
    gl.init_app(self.app)
    # From now on, when using standard logging in the app code
    # Log messages are stored in a Google Cloud Project
    # Defined by the app configuration 

```


6. App configuration keys used by GoogleCloudLogManager class
```console
   # Application display name
    FLASK_APP_DISPLAY_NAME = os.environ.get('FLASK_APP_DISPLAY_NAME') or 'gcpLogDemo'
    
   # Google Cloud Logging service account key json file
   # Determines service account and hence GCP project where logs are stored
    LG_SA_KEY_JSON_FILE = os.environ.get('LG_SA_KEY_JSON_FILE') or '/etc/secrets/sa_key_lg.json'

    # Google Cloud Log configuration, preferably related to app name and environment
    # Must follow Google Cloud log names constraints
    # A new log will be created in Google Cloud Project with this name, which is  needed to search logs for this 
    # application in Logs Explorer
    GC_LOGGER_NAME = os.environ.get('GC_LOGGER_NAME') or FLASK_APP_DISPLAY_NAME
    
    # Descriptor that refers to app in logs
    APP_LOG_ID = os.environ.get('APP_LOG_ID') or FLASK_APP_DISPLAY_NAME

 ```

7. Running the application locally.  
   * Revise how to set up a python development environment and Google Cloud project as described in the previous post  
   [GCP python quickstart](2023-07-27-gcp-python-quickstart.md)  
   
  * Instead of creating your own repo, clone the demo git repo from Github
  ```console
  git clone [repo_link]
   ```
  * Create a service account and key as described in previous [Access to GCP resources](2023-07-31-access-to-GCP-res.md) 
  * Remember to assign the IAM role Logs Writer to the SA during creation.
  * Edit the [application configuration](config.py repo link) to update the key LG_SA_KEY_JSON_FILE with the SA key file path    


8. Here is a little demo: [Demo App](link)
