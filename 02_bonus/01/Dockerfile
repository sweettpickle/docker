FROM ubuntu:latest
RUN apt-get update  
RUN apt-get install -yq --no-install-recommends curl ca-certificates   
ENV SITE_URL https://google.com/  
RUN mkdir data && touch /data/results  
CMD sh -c "curl -L $SITE_URL > /data/results"

