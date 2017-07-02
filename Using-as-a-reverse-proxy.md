Script server can be used under Reverse proxy.

Example nginx configuration:
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
* websocket section is needed to make possible of reverse proxying websockets into http
