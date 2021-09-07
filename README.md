# README

1. Create a Dockerfile

```
FROM ruby:latest

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

RUN apt-get update -yqq && apt-get install -y build-essential libpq-dev
RUN apt-get install -y nodejs 

WORKDIR /usr/src/app

COPY Gemfile /usr/src/app/Gemfile
COPY Gemfile.lock /usr/src/app/Gemfile.lock

RUN bundle install

COPY . /usr/src/app
```

2. Create a Gemfile:

```
source 'https://rubygems.org'
gem 'rails'
```

3. Create an empty Gemfile.lock file.

4. Build the image:

```
docker build -t rob .
```

The image is tagged as rob.

5. Create a API only Rails app:

```
docker run -it --rm -v ${PWD}:/usr/src/app rob rails new myapp --api --skip-test --skip-bundle
```

6. Change the owner of the application files from root to user.

```
sudo chown $USER:$USER -R myapp
```

7. Run the app:

```
docker run --rm -it -p 3000:3000 rob
```

8. List Rake tasks:

```
docker run --rm rob rails -T
```

9. Using the rails console:

```
docker run --rm -it rob rails c
```

10. To avoid gem install during docker build when Gemfile has not changed, go to the project root folder and run:

```
docker run --rm -it -w /usr/src/app/myapp rob bundle lock
```

After running this command build the image, any build after this will not run bundle install if the Gemfile has not changed.

