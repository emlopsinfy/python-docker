followed this  
https://docs.docker.com/language/python/develop/  

First create python app:  
app.py has all code changes to connect to mysql image which runs in separate container.  
docker build . -t python-docker  
docker run -d -p 5000:5000 --name rest-server python-docker  
now python app is running..  

Second pull mysql image and run in container and give volumes and network  
before that, create volumes and network first..  
docker volume create mysql  
docker volume create mysql_config  
docker network create mysqlnet  

Then run this..  
docker run --rm -d -v mysql:/var/lib/mysql -v mysql_config:/etc/mysql -p 3306:3306 --network mysqlnet --name mysqldb -e MYSQL_ROOT_PASSWORD=p@ssw0rd1 mysql  
you are pulling mysql image, mapping created volumes and network, giving container name as mysqldb, and passwords.. 
docker exec -ti mysqldb mysql -u root -p  
for inspecting mysql container..  

your app.py has homeapi, /initdb, /widgets api..  
home api work with out any issue now..  
/initdb and /widgets wont work because your python app not running in mysql network which is mysqlnet..  

Re-run the python app mentioning the same network..  
docker build . -t python-docker-dev (building again)  
docker run --rm -d --network mysqlnet --name rest-server -p 5000:5000 python-docker-dev  (stop your first container before doing this..)  

Now your python app can talk to mysql DB..!  
Both are running in different containers, but in same network, mysql uses volumes for persisting data..  

NEXT STEP:  
Using docker-compose to run everything with single command...  




