Script server can be used behind Reverse proxy. In order to work properly, there are the following requirements to proxy setup:
* websocket upgrade should be enabled  
* path replacement should be configured properly (for direct calls and redirect responses)  
* it's better to add proxy IP to [[trusted_ips|Server-configuration#--trusted_ips]] for correct user IP resolving. Also the proxy should fill X-Forwarded-For or X-Real-IP header  

You can find configuration examples for popular reverse proxies below:  
* [nginx](#nginx)
* [Apache](#apache)


## Nginx
```javascript
location ^~ /script-server/ {
	proxy_pass_header Server;
	proxy_set_header Host $proxy_host;
	proxy_set_header X-Real-IP $remote_addr;
	proxy_set_header X-Scheme $scheme;
	proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
	proxy_pass http://127.0.0.1:5000/;
	
	# needed for websockets
	proxy_http_version 1.1;
	proxy_set_header Upgrade $http_upgrade;
	proxy_set_header Connection "upgrade";
	proxy_set_header Origin http://$proxy_host;
}
```
Important parts of the configuration:
* Host header can be omitted. And it should always be script-server real address in order to properly make redirections
* proxy_pass should end with /. It's needed for nginx to perform path replacement, otherwise all html links become incorrect  

## Apache
The following apache mods should be installed and **activated**:
* proxy
* proxy_http
* proxy_wstunnel
* rewrite
* headers  

Configuration section for VirtualHost:
```xml
<Location /script-server/>
    Define ScriptServerUrl http://localhost:5000/

    ProxyPass ${ScriptServerUrl}
    ProxyPassReverse ${ScriptServerUrl}
    RequestHeader set Origin ${ScriptServerUrl}

    RewriteEngine On
    RewriteCond %{HTTP:UPGRADE} ^WebSocket$ [NC]
    RewriteCond %{HTTP:CONNECTION} Upgrade$ [NC]
    RewriteRule /(.*) ws:/$1 [P,NE]
</Location>
```  
Change `ScriptServerUrl` if needed.
