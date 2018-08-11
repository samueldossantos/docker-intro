#Dockerfile - Image creation optimization    

Create two docker images, based on Ubuntu that creates a non-optimized and an optimized image to run an implementation of the ethereum protocol in go. 

Using the following commands, two images are created and a quick size comparison can be made:

    #Build a non optimized image
    $ docker build -t ethereum_xenial:0.1 .


    #Build an optimized image
    $ docker build --no-cache -t ethereum_xenial:0.2 .

 
    $ docker images
    REPOSITORY  TAG IMAGE IDCREATED SIZE
    ethereum_xenial 0.1 4f55efed8a05About an hour ago   745MB
    ethereum_xenial 0.2 f0c5f3813b5d2 hours ago 289MB