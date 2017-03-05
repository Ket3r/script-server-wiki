You can edit conf/conf.json file, if you want to add some features to the server, of want to change default values. 

conf.json structure:
```javascript
{
  /**
    * Required: no
    * Description: custom port for running the web server
    * Type: number
    * Default: 5000 (5443 for ssl)
    */
  "port": 8080,
  /**
    * Required: no
    * Description: add ssl element to configure ssl, if needed
    * Type: object
    */
  "ssl": {
    /**
      * Required: yes
      * Description: the path to key file. Can be relative to script-server location
      * Type: string
      */
      "key_path": "testing/ssl/script-server.key",
    /**
      * Required: yes
      * Description: the path to cert file. Can be relative to script-server location
      * Type: string
      */
      "cert_path": "testing/ssl/script-server.crt"
  }
}
```
