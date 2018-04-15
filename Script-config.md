Each script should be configured in conf/runners/_conf-name_.json file, where _conf-name_ is an unique name for each script. Each configuration file should contain only one script.

Example of configuration files can be found in [master/testing/configs](../blob/master/samples/configs)

Here is a descripton of all possible elements in a configuration file:
```javascript
{
  /**
    * Required: no
    * Description: user-friendly script name. Will be displayed to user
    * Type: string
    * Default: script_path filename without extension
    */
  "name": "My example script",
  /**
    * Required: YES
    * Description: path to the script (relative to working directory) OR just a command.
                   WINDOWS IMPORTANT: any non-batch scripts should be run as a command, e.g. 'python my_script.py'
    * Type: string
    */
  "script_path": "/some/path/to/script.sh",
  /** or "script_path": "python my_script.py" */
  /**
    * Required: no
    * Description: user-friendly script description, which will be shown to a user
    * Type: string
    */
  "description": "Some useful description on what the script does",
  /**
    * Required: no
    * Description: specifies, if the script should be run in pseudo terminal (if it has special behaviour in terminal). This works only for Linux, on other operating systems this flag will be ignored.
    * Type: boolean
    * Default: true
    */
  "requires_terminal": true,
  /**
  * Required: no
  * Description: allows to specify working directory of the script.
  * Type: string
  * Default: the working directory, from which the server was started
  */
  "working_directory": "/home/me/temp",
  /**
  * Required: no
  * Default: true for linux and mac, false otherwise
  * Description: enables bash color parsing and sending colored output to the user interface. E.g. \033[01;31m is a bold red text
  * Type: boolean
  */
  "bash_formatting": true,
  /**
  * Required: no
  * Description: list of files, which will be downloadable by user after a script execution, can be:
      - file path, with:
          - * and ** wildcard support
          - substitution of script parameters with $$$parameter_name (e.g. /home/me/$$$f1.txt, when f1=readme, becomes /home/me/readme.txt)
      - regex pattern, for searching file path in script output. Should start with #, 
              Matching group number can be specified with number# (e.g. #2#). 
              #any_path can be used as analogue to ** in normal path specification
  * Type: array
  */
  "output_files": [
    "##any_path/localhost.*.log",
    "~/simple.txt"
  ],
  /**
    * Required: no
    * Description: list of script parameters
    * Type: array
    */
  "parameters": [
    {
      /**
        * Required: no
        * Description: the name of the parameter, which will be shown to the user. Required for non-constant parameters
        * Type: string
        */
      "name": "MyParam",
      /**
      * Required: no
      * Description: can be used for specifying script parameter name (e.g. script.sh -p myval). Omit this field for position based parameters
      * Type: string
      */
      "param": "-p",
      /**
      * Required: no
      * Description: if true, then no value will be passed to the script, only "param" will be specified.
                     Makes parameter type to be boolean
      * Type: boolean
      * Default: false
      */
      "no_value": false,
      /**
     * Required: no
     * Description: user-friendly description of the parameter, shown to the user (not yet implemented in GUI)
     * Type: string
     */
      "description": "This parameter is used for filename",
      /**
      * Required: no
      * Description: if the value of the parameter is required
      * Type: boolean
      * Default: false
      */
      "required": false,
      /**
      * Required: no
      * Supported types: text, int, boolean, list
      * Description: default value shown to user
      * Type: string
      */
      "default": "empty",
      /**
      * Required: no
      * Description: parameter type. Important: if no_value, then type is ignored and always boolean
                     - In case of file_upload, user file is uploaded to the server 
                       and then passed to a script as an absolute path
      * Default: "text"
      * Allowed values: int, list, text, file_upload
      * Type: string
      */
      "type": "int",
      /**
      * Required: no
      * Supported types: text, boolean
      * Description: don't show parameter to user, but fill it in the script with the value of "default" field
      * Type: boolean
      * Default: false
      */
      "constant": false,
      /**
      * Required: no
      * Supported types: int
      * Description: upper value bound
      * Type: string
      */
      "max": "50",
      /**
      * Required: no
      * Supported types: int
      * Description: lower value bound
      * Type: string
      */
      "min": "-1",
      /**
      * Required: no
      * Description: don't show value on server anywhere, replace it with a mask
      * Type: boolean
      * Default: false
      */
      "secure": true,
      /**
      * Required: no
      * Supported types: list
      * Description: list type only, array of allowed values for the parameter. Can be either predefined values or result from script invocation
      * Type: array or object
      */
      "values": [
        "Apple",
        "Orange",
        "Banana"
      ]
      /** or "values": { "script": "ls /home/me/projects" } */
    }
  ]
}
```
