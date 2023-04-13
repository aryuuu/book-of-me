# Budget Ngrok with Static URL

04/13/2023

----

Are you tired of updating your webhook url to the one provided by [ngrok](https://ngrok.com/) over and over again?

Do you have a VPS ready to use? along with domain name perhaps?

If you check all the categories above, maybe this article is for you.

I had this case where I have to run a service locally and hit it from a [Retool](https://retool.com/) app running on cloud.
The amount of time where I have to update the url to my local service in Retool is driving me nuts (okay maybe I'm being dramatic here).

So like a true developer, I decided to spend couple hours doing research on SSH tunneling instead of taking a few seconds to update the url.

## SSH Tunneling

Let's start our journey by reading the friendly manual of SSH

There we can find this section about port forwarding

```sh
...
     -R [bind_address:]port:host:hostport
     -R [bind_address:]port:local_socket
     -R remote_socket:host:hostport
     -R remote_socket:local_socket
     -R [bind_address:]port
             Specifies that connections to the given TCP port or Unix socket on the remote (server) host are to be forwarded to the local side.
...
```

Sweet, looks like there is something that we can use here. Let's forward whatever port we want in our localhost to port 4000 in remote host

```sh
ssh -NR receptor.aryuuu.com:4000:localhost:[port] linode-aryuuu
```

Let's break down what these flags and arguments mean.

- `-N`: do not execute remote commands, useful for port forwarding. Without this flag we are going to see a lot of noise (might be good if you need it).
- `-R`: bind address.
- `receptor.aryuuu.com`: address of the remote host.
- `4000`: the port on the remote host I want my port to be forwarded to.
- `localhost`: localhost.
- `[port]`: port on localhost that I want to forward to the remote host.
- `linode-aryuuu`: address of the remote host, I just happen to have this name configured for my VPS in my ssh config.

I put it in my utils function collection, you can check the final function here.

```sh
expose () {
	if [[ -z $1 ]]; then
		echo "Please input source port"	
	else
		ssh -NR receptor.aryuuu.com:4000:localhost:$1 linode-aryuuu
	fi
}
```

Now I can use this by using this command

```sh
$ expose [port]
```

## Reverse Proxy Setup in Nginx

Okay, now we have the command to forward the port ready. But the url that we can use is kinda ugly. It will look like something like this.

```sh
receptor.aryuuu.com:4000
```

Let's make it a bit more pretty.

Now we need to somehow direct traffic incoming to `receptor.aryuuu.com` to port `4000` of the remote host.
Let's configure the nginx on the remote host just like this.

```sh
server {                                  
    server_name receptor.aryuuu.com;      
                                          
    location / {                          
        proxy_pass http://localhost:4000; 
    }                                     
}
```

We could just use port 80 on the remote host, but screw that. I want to use that port for some other purpose.

Anyway, there you have it. Now I can "expose" my local port just by running this command

```sh
expose [port]
```

And now it is accessible from the internet using this URL 

```sh
receptor.aryuuu.com
```

