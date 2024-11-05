# GSP055
[![]()](https://www.youtube.com/@CloudHustlers)
## Run in cloudshell
```cmd
export REGION=
```
```cmd
mkdir test && cd test
cat > Dockerfile <<EOF
# Use an official Node runtime as the parent image
FROM node:lts

# Set the working directory in the container to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Make the container's port 80 available to the outside world
EXPOSE 80

# Run app.js using node when the container launches
CMD ["node", "app.js"]
EOF
cat > app.js << EOF;
const http = require("http");

const hostname = "0.0.0.0";
const port = 80;

const server = http.createServer((req, res) => {
	res.statusCode = 200;
	res.setHeader("Content-Type", "text/plain");
	res.end("Hello World\n");
});

server.listen(port, hostname, () => {
	console.log("Server running at http://%s:%s/", hostname, port);
});

process.on("SIGINT", function () {
	console.log("Caught interrupt signal and will exit");
	process.exit();
});
EOF
docker build -t node-app:0.1 .
docker run -p 4000:80 --name my-app -d node-app:0.1
cd test
sed -i 's/Hello World/Welcome To Cloud Hustlers/g' app.js
docker build -t node-app:0.2 .
docker run -p 8080:80 --name my-app-2 -d node-app:0.2
gcloud artifacts repositories create my-repository --repository-format=docker --location=$REGION --description="Docker repository"
docker build -t $REGION-docker.pkg.dev/$DEVSHELL_PROJECT_ID/my-repository/node-app:0.2 .
docker push $REGION-docker.pkg.dev/$DEVSHELL_PROJECT_ID/my-repository/node-app:0.2
