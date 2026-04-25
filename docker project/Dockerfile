FROM python:latest 

# building the Docker Image
# docker build -f Dockerfile -t pyapp .
# Runnig the Image
# docker run -it pyapp

# Just for confirmation: docker login

# can't push to my repos, it fails : docker push pyapp

# To push you docker file you need the below synatx
# docker build -f Dockerfile -t account_name/image_name:latest .
# then using push we can push the docker image to the docker hub repositories on to my personal account
# docker push account_name/image_name:latest    

# To run a terminal where my code resides you can just use the below commands
# docker run -it account_name/image_name:latest /bin/bash
# python 
# execute some python commnads
# quit()
# exit

# We can run these files on a certain server port
# First after writing the below command in the Dockerfile : CMD ["python","-m","http.server","8000"]
# Build the image again
# docker build -f Dockerfile -t pyapp .
# docker run -it pyapp
# But it will fail, because the container is runnig in a container.
# Try this : docker run -it -p 3000:8000 pyapp
# or
# docker run -it -p 8000:8000 pyapp

CMD ["python","-m","http.server","8000"]
