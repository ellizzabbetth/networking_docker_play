

Build image
    docker build -t favorites-node: latest .

Run container with name and detached mode;
    docker run --name favorites -d -p 3000:3000 favorites-node


View stoped containers
    docker ps -a

View containers
    containers

What happened? How to debug? Run in attached mode:
    docker run --name favorites -p 3000:3000 favorites-node

No database.

Run mongodb:
    docker run -d --name mongodb mongo

docker container inspect mongodb to get "IPAddress": "172.17.0.2",

mongoose.connect(
  'mongodb://<IP_ADDRESS>:27017/swfavorites',
  { useNewUrlParser: true },
  (err) => {
    if (err) {
      console.log(err);
    } else {
      app.listen(3000);
    }
  }
);


74: Networks
Create network
    docker network create favorites-net
Add mongodb to network
    docker run -d --name mongodb --network favorites-net mongo
Add node to network
    docker run --name favorites --rm -d -p 3000:3000 --network favorites-net favorites-node
Use container name as domain => <container> = mongodb
  'mongodb://mongodb:27017/swfavorites',

docker logs favorites
Verify in Postman
    localhost:3000/favorites returns 200

NOTE

When I run the MongoDB container, I don't have the -p option here. The reason for that is that the -p option is only required
if we plan on connecting to something in that container from our local host machine or from outside the container network.

docker run -d --name mongodb --network favorites-net mongo

Now, in this case, we don't do that. The only thing which connects to this MongoDB container will be our favorites container,
which will be part of the same Docker network, and in such cases, when you have a container to container connection,
then you don't need to publish the port because internally in that container network,
all the containers can freely communicate with each other and you don't need to expose any ports.

    docker run --name favorites --rm -d --network favorites-net favorites-node
    docker run --name favorites --rm -d -p 3000:3000 --network favorites-net favorites-node