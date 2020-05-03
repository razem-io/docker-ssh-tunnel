[![](https://images.microbadger.com/badges/image/razemio/docker-ssh-tunnel.svg)](https://microbadger.com/images/razemio/docker-ssh-tunnel)

# Docker SSH Tunnel

This Docker creates a simple SSH tunnel over a server. It is very useful when your container needs to access to an external protected resource. In this case this container might behave like a proxy to outer space inside your Docker network.

## Usage

1. First you should create a config file in your local directory. For simplicity you can create this file in `~/.ssh` in your local machine.

2. Inside `~/.ssh/config` put these lines:

```
Host tunnel_host # You can use any name
  HostName 1.2.3.4 # Tunnel 
  IdentityFile ~/.ssh/id_rsa # Private key location
  User razemio # Username to connect to SSH service
  ForwardAgent yes
  TCPKeepAlive yes
  ConnectTimeout 5
  ServerAliveCountMax 10
  ServerAliveInterval 15
```

3. Don't forget to put your private key (`id_rsa`) to `~/.ssh` folder.

4. Now in `docker-compose.yml` you can define the tunnel as follows:

```
    version: '2'
    services:
      mysql:
        image: razemio/docker-ssh-tunnel:latest
        volumes:
          - $HOME/.ssh:/root/ssh:ro
        environment:
          TUNNEL_HOST: tunnel_host
          REMOTE_HOST: localhost
          LOCAL_PORT: 3306
          REMOTE_PORT: 3306
```

5. Run `docker-compose up -d`

After you start up docker containers, any container in the same network will be able to access to tunneled mysql instance using ```tcp://mysql:3306```. Of course you can also expose port 3306 to be able to access to tunneled resource from your host machine.