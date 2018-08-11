FROM ubuntu:xenial

ENV PATH=/usr/lib/go-1.10/bin:$PATH
ENV ETHEREUM_REPO https://github.com/ethereum/go-ethereum

RUN apt-get update                              &&\
    apt-get upgrade -q -y                       &&\
    apt-get install -y --no-install-recommends    \
    ca-certificates                               \
    gcc                                           \
    git                                           \
    golang-1.10                                   \
    libc-dev                                      \
    make    

#Clone and build        
RUN git clone --depth 1 ${ETHEREUM_REPO}        &&\
    cd /go-ethereum                             &&\
    make geth                                   &&\
    cp /go-ethereum/build/bin/geth /geth         
    
#Cleanup    
RUN apt-get remove -y                             \
            golang-1.10                           \
            git                                   \
            make                                  \
            gcc                                   \
            libc-dev

            
#Uninstall automatically installed software not 
#required by other software            
RUN apt-get autoremove -y

#Remove downloaded packages
RUN apt-get clean                               &&\
    rm -rf /go-ethereum


EXPOSE 8545
EXPOSE 30303

ENTRYPOINT ["/geth"]