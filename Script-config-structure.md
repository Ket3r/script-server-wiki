Script should be configured in conf/runners/_conf-name_.json file, where _conf-name_ is an unique name for your configuration. 

Description of the file structure:

      * Description: if the value of the parameter is required 
      * Type: boolean
      * Default: false
      */
      "required": false,
      /**
      * Required: no
      * Description: default value shown to user
      * Type: string
      */
      "default": "empty",
      /**
      * Required: no
      * Description: don't show parameter to user, but fill it in the script with the value of "default" field
      * Type: boolean
      * Default: false
      */
      "constant": false,
      /**
      * Required: no
      * Description: parameter type. Allowed values: int, list. Any other value will be simple text edit.
      * Type: string
      */
      "type": "int",
      /**
      * Required: no
      * Description: int type only, upper value bound 
      * Type: string
      */
      "max": "50",
      /**
      * Required: no
      * Description: int type only, lower value bound 
      * Type: string
      */
      "min": "-1",
      /**
      * Required: no
      * Description: list type only, array of allowed values for the parameter. Can be either predefined values or result from script invocation
      * Type: array or object
      */
      "values": [ "Apple", "Orange", "Banana" ]
      /** or "values": { "script": "ls /home/me/projects" } */
  }
  ]
}
```