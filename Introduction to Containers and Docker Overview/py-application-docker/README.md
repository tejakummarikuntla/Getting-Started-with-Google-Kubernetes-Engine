ocker image will function the same way on all docker supported OSes (OS X, Windows & Linux).

Try accessing the webserver again - <a href="http://localhost:8888/" target="_blank">http://localhost:8888</a>

Now let's upload the docker image to your private image repository in Google Cloud Registry (`gcr.io`).

1. Store the GCP project name in an environment variable
```shell
export GCP_PROJECT=<your-project-name>
```

2. Rebuild the docker image with an image name that includes `gcr.io` project prefix.
```shell
docker build -t "gcr.io/${GCP_PROJECT}/py-web-server:v1" .
```

3. Push the image to `gcr.io`
```shell
gcloud docker push -- gcr.io/${GCP_PROJECT}/py-web-server:v1
```

The image is now private and people with access to `$GCP_PROJECT` used above can only access it.

Now, let's make the image accessible to everyone.
Google Container Registry stores its images on Google Cloud storage.
Let's update the permissions on Google Cloud Storage to make our image repository publically accessible.

```shell
gsutil defacl ch -u AllUsers:R gs://artifacts.${GCP_PROJECT}.appspot.com
gsutil acl ch -r -u AllUsers:R gs://artifacts.${GCP_PROJECT}.appspot.com
gsutil acl ch -u AllUsers:R gs://artifacts.${GCP_PROJECT}.appspot.com
```

The docker image can now be executed from any machine by running the following command
```shell
docker run -d -p 8888:8888 -h my-web-server gcr.io/${GCP_PROJECT}/py-web-server:v1
```

Detailed Dockerfile reference is available [here](https://docs.docker.com/engine/reference/builder/).
