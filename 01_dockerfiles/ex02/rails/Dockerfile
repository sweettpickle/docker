FROM ruby:2.4.1
RUN apt-get update -y && apt-get upgrade -y && apt-get install -y build-essential nodejs sqlite3 

ONBUILD COPY myapp /opt/app
ONBUILD WORKDIR /opt/app

ONBUILD EXPOSE 3000
ONBUILD RUN bundle install
ONBUILD RUN rake db:migrate
ONBUILD RUN rake db:seed
