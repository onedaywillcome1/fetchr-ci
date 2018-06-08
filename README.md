## Fetchr CI-CD Big Picture
![Alt text](docs/fetchr-cicd.png?raw=true "CI-CD")

## CI-CD Steps
- **Push to github**: Developer who works in Fetchr pushes his code dev/master branch into Github.
- **Checkout**: After configuring Jenkins pipeline job, jenkins polls the github repo periodically.When new code is pushed to Github, job is triggered.
- **Run tests**: After checking out the code, jenkins runs the tests with maven command.
- **Build**: If tests are passsing, maven packages jar/war, then docker image is being created with fresh jar/war.
- **Publish**: After building image, Jenkins publishes image to docker registry with its tag.
- **Deploy**: When new image pushed to registry, Jenkins deploys pushed image to app server(i.e.dev) with ansible.


## Jenkins Manual Steps
After Jenkins installed automatically with Ansible & Vagrant, there are some manual steps to do(https://github.com/onedaywillcome1/fetchr-infrastructure)

- Install pipeline & git plugins from "Manage plugins" part.
![Alt text](docs/pipeline.png?raw=true "Pipeline")
![Alt text](docs/git.png?raw=true "Git")
- Maven is auto installed from "Global Tools configuration" and called M2
![Alt text](docs/maven.png?raw=true "M2")
- Docker USER and Docker PASS is provided as an environment variable when it is needed for publishing.
![Alt text](docs/env-vars.png?raw=true "Env-Var")
- Create Pipeline job and Checkout from SCM with https://github.com/onedaywillcome1/fetchr-ci/blob/master/ci-cd-pipeline/pipeline.jenkinsfile
![Alt text](docs/pipeline1.png?raw=true "Pipeline1")
![Alt text](docs/pipeline2.png?raw=true "Pipeline2")

- General Pipeline view is as below:
![Alt text](docs/pipeline3.png?raw=true "Pipeline3")

- App is running in appserver(dev) as follow:
![Alt text](docs/app.png?raw=true "Pipeline3")

- Image is published to registry(https://hub.docker.com/r/onedaywillcome/fetchr-sample-springboot/)
![Alt text](docs/registry.png?raw=true "Registry")


## How do we manage Deployment Versions?
* In a real-world CI-CD pipelines;
    * When developers pushes code to dev branch, it pushes image with "snapshot" postfix with "versioning" number(1.4.0-snapshot).
    * When developers merges code to master branch from dev branch, it pushes image with "versioning" number(1.4.0).
* Semantic versioning number(1.4.0) can be described as below:
    * Most left number can represent major release(1)
    * Middle number can represent minor release(4)
    * Most right number can represent hotfix(0)
* Semantic versioning can be controlled by setting up versioning server. There is a really good project about that: https://github.com/Volgaa/semantic-versioning-server
    * After installing versioning server, versions of projects can be kept in server with "post request" after deployment.
    * curl -X POST -d version_type=major http://versioningserver:8000/project/myProject/incrementVersion


## How do we improve Continuous deployment?
* Instead of using docker-compose or directly docker containers to deploy our apps, container orchestration tools(like Kubernetes, AWS ECS, docker swarm) can be used
* Kubernetes or etc. tools will provide scalability, zero down time deployment(blue-green) and high-availability.
