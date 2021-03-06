Armycreator contributing documentation 
============

This repository tell us how to develop for armycreator website.

## How to communicate
Slack: [armycreator](https://armycreator.slack.com/)

## Dependencies
The armycreator website is managed by docker to avoid the complexity of installing a lots of dependencies.
The only one (well two) you need is [docker and docker-compose](https://docs.docker.com/compose/install/)

## Installation

* Clone the [containers repository](https://github.com/armycreator/containers) in a directory (like `~/code/armycreator-containers`)
* Clone [armycreator website repository](https://github.com/armycreator/armycreator-website) in the same root directory (`~/code` in our example) and name it `armycreator-website` (it should be in `~/code/armycreator-website`)
* In the `armycreator-containers` folder, execute:
  * `docker-compose build`
  * `docker-compose up`
  * `./build.sh init`

## Launch website
Open `http://localhost:81`.

You should see the armycreator homepage.

Connect to site with user and password: `admin` / `admin`

## Want to access the API ?
The domain name will be `http://api.127.0.0.1.xip.io:81` and `http://oauth2.127.0.0.1.xip.io:81`

### Get a valid access token 
```sh
curl -X POST  http://oauth2.127.0.0.1.xip.io:81/oauth/v2/token \
  -F 'client_id=1_1fmv6y40q7r440oc48sswwc40sos88ww408088o0c8g8wko40c' \
  -F 'client_secret=5qquyxdde4g0g8kkwcgso40kk4gcwckg44wgks0484ko8cs0o' \
  -F 'grant_type=password' \
  -F 'username=admin' \
  -F 'password=admin'
```
or with a tool like Postman by selecting "POST" method and form-data in body

Client id and client secret are valid in development only.

The access token is valid one hour.

The results will looks like this:
```json
{
  "access_token": "MzllYTgyMDg2MmEzYWIwYzVlYWExZjExMTcxNzIwNWE3ZmMyMjllNzQxMWM4MDBiODBkNGI5MjE1Zjg4YTYzYg",
  "expires_in": 3600,
  "token_type": "bearer",
  "scope": null,
  "refresh_token": "YTJkNmQ3YzA1OWY0YjQ1ZjYxMWUxOWQyZGQxOTY1MTRiN2I4OTFkNmJhMjcxYmU3ZDg4NDEyNTgyZWRhOTRlYg"
}
```

Every request you will make to the API will need to have an "Authorisation" header with value `Bearer ValueOfTheAccessTokenKey`

## How do I manage assets, cache, where is the symfony command, etc.
As everything is managed by docker and docker-compose, you can do everything with them, but the `build.sh` file is here to help you do anything you want.

For exemple, if you want to run `composer require foo/bar`, you can just run `./build.sh composer require foo/bar`, `./build.sh npm install` for installing npm dependencies, `./build.sh sf cache:clear` for clearing symfony cache, etc.

If a command is missing, the best thing to do is to look into the `build.sh` file.


## Troubleshooting
### Accessing MySQL
If you want to access MySQL from your machine (with a software for exemple), just look in the `docker-compose.yml` file.

### Port issues
The http server and MySQL needs to have a opened port to access them.

#### Port conflict is taken
The http server uses the port number `81`.
If you need to use another port, you can just run docker compose like this: `HTTP_PORT=82 docker-compose up`.


### mysql permission denied
Having a problem like this:
```sh
db_1      | chown: cannot read directory '/var/lib/mysql/': Permission denied
apache_1  | (13)Permission denied: AH00091: apache2: could not open error log file /var/log/apache2/error.log.
apache_1  | AH00015: Unable to open logs
containers_db_1 exited with code 1
containers_apache_1 exited with code 1
```

It may be a problem with SELinux, you can read [this blog post](http://www.projectatomic.io/blog/2015/06/using-volumes-with-docker-can-cause-problems-with-selinux/) about it

You can do something like this:
  * `chcon -Rt svirt_sandbox_file_t /the/path/to/your/container-folder`
  * `chcon -Rt svirt_sandbox_file_t /the/path/to/your/website-folder`
  
### Want to remove everything docker-related ?
```sh
# Delete all containers
docker rm $(docker ps -a -q)
# Delete all images
docker rmi $(docker images -q)
```
