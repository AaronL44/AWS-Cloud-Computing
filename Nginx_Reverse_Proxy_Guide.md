# Nginx Reverse Proxy Guide

## What is Nginx?
- It's like a gateway thats stands between the internet and your backend infastructure (code).
  - When you visit a web app the first place your request goes is to a web server.
    - It's job is to look at the requested resource and determine where to find it on the server. Then send it back as the response! 
- Very popular among high traffic sites as it can handle over 10,000 simultaneous connections.
- Also used as a Reverse Proxy where it acts as a traffic light to distribute the load to multiple backend servers, while also providing security.

## What is a reverse proxy? 
- A reverse proxy is essentially the middleman between the internet and your actual web server.
  - Example:
    - A reverse proxy (like Nginx) takes a user’s request (e.g., opening a website).
    - It forwards the request to the correct backend server (e.g., a Node.js app or database).
    - The backend server processes it and sends the response back through the reverse proxy to the user.
  - Analogy:
    - You go to a restaurant and order food. Instead of going directly to the chef, you give your order to a waiter.
    - The waiter (reverse proxy, Nginx) takes the order (user request) and decides where to send it.
    - The chef (Node.js) prepares the food (processes the request, fetches data, runs backend logic).
    - The waiter (Nginx) then brings the food back to the customer (the user’s browser).
#### How this would benefit our sparta app:
-  Normally, when you want to access a service on a specific port (like http://example.com:8080), the port number tells the browser which service to connect to.

With a reverse proxy, you can hide the port number from the user. For example, you can set up the reverse proxy to listen on the standard HTTP port (80) or HTTPS port (443), so users only need to access http://example.com or https://example.com without worrying about the port number.
### Why implement one?
- Security: Hides backend servers from the public.
- Load balancing: Distributes traffic across multiple servers to prevent overload.
- Caching: Speeds up responses by storing frequently accessed data.

## How are they different to a forward proxy?
- A forward proxy sits in front of users, handling and forwarding outgoing requests to the internet.
- Provides a layer of security in that your identity is unkown. Only the forward proxy knows who the request comes from and goes to.
- Analogy:
  - Instead of going directly to the restaurant, you send your assistant (forward proxy) to order food for you.
  - The restaurant doesn’t know who you are—only the assistant does.
  - The assistant gets the food and brings it back to you.

## How do they work?
### Reverse Proxy:
![Reverse Proxy Diagram](/AWS-Cloud-Computing/images/rp%20diagram.png)


# Implementing a reverse proxy

## Manually

- Launched the application using the automated script. 

- Copied the server address.

- Found the config file for nginx. I used sudo before nano as this would allow me to save changes.
- `sudo nano /etc/nginx/sites-available/default`
  - sudo = Used due to permission problems when editing config files.
  - /etc = configuration files.
- Navigated to "location" block in the config file.
- location / {
    ``proxy_pass http://http://localhost:3000/``
        }
- Saved the config file.
- Restarted nginx using the command:
  - `sudo systemctl restart nginx`
- The app now loads using the reverse proxy and therefore doesn't need the port number after the public ip address.

# Automated 

- Added the following to the app script:
- `sudo sed -i '51c\proxy_pass http://0.0.0.0:3000;' /etc/nginx/sites-available/default`
- Explaining the line of code:
  - `sudo sed` stands for stream editor - performs basic text transformations on an input stream.
  - `-i` means edit in place. This tells sed to directly modify the file rather than just printing the changes to the terminal.
  - `51c` tells sed to change (with the c command) the line at line 51 in the file.
  - `\` seperates the command.
  - `proxy_pass http://0.0.0.0:3000;` This is the text that will replace the entire contents of line 51. It sets up a reverse proxy for NGINX to forward requests to the application running on http://0.0.0.0:3000.
  - `/etc/nginx/sites-available/default` This is the path to the default site configuration file for NGINX.
- Saved the changes and ran the script on a new instance which worked.