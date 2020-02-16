FROM ruby:2.6.5-stretch
ENV LANG C.UTF-8

ENV RAILS_ENV=development
ENV APP_ROOT /app
ARG RAILS_MASTER_KEY
ENV RAILS_MASTER_KEY ${RAILS_MASTER_KEY}

WORKDIR $APP_ROOT

RUN apt-get update && apt-get install -y --no-install-recommends \
    curl apt-transport-https wget && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
    curl -sL https://deb.nodesource.com/setup_8.x | bash - && \
    apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    vim \
    mysql-client \
    yarn \
    nodejs && \
    gem install bundler && \
    rm -rf /var/lib/apt/lists/*

COPY Gemfile $APP_ROOT
COPY Gemfile.lock $APP_ROOT
RUN bundle install
RUN bundle exec rails assets:precompile

# Expose volumes to frontend
VOLUME $APP_ROOT/public
VOLUME $APP_ROOT/tmp

COPY . $APP_ROOT

ARG RAILS_MASTER_KEY
ENV RAILS_MASTER_KEY ${RAILS_MASTER_KEY}

EXPOSE 3000