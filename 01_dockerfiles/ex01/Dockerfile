FROM debian

ENV TS3SERVER_LICENSE=accept

EXPOSE 9987/udp 10011 30033

RUN apt-get update -y && apt-get upgrade -y && apt-get install wget -y && apt-get install bzip2 -y && wget https://files.teamspeak-services.com/releases/server/3.10.2/teamspeak3-server_linux_amd64-3.10.2.tar.bz2 && tar xvf teamspeak3-server_linux_amd64-3.10.2.tar.bz2

WORKDIR teamspeak3-server_linux_amd64 

CMD sh ts3server_minimal_runscript.sh
