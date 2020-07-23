FROM ruby:2.6.3

# Use UTF-8
ENV LANG C.UTF-8

# Install node.js, yarn and other applications
ENV DEBCONF_NOWARNINGS yes
ENV APT_KEY_DONT_WARN_ON_DANGEROUS_USAGE=DontWarn
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash - \
  && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
  && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
  && apt-get update -qq \
  && apt-get install -y --no-install-recommends \
    nodejs \
    postgresql-client \
    vim \
    yarn \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

# Install Entrykit
ENV ENTRYKIT_VERSION 0.4.0
RUN wget -q https://github.com/progrium/entrykit/releases/download/v${ENTRYKIT_VERSION}/entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
  && tar -xf entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
  && rm entrykit_${ENTRYKIT_VERSION}_Linux_x86_64.tgz \
  && mv entrykit /bin/entrykit \
  && chmod +x /bin/entrykit \
  && entrykit --symlink

# Configure Bundler
RUN bundle config build.nokogiri --use-system-libraries

# Update default gems
RUN gem update --system \
  && gem update bundler \
  && gem update

# Specify work directory
WORKDIR /opt/railsapp

ENTRYPOINT [ \
  "switch", "install=bundle install --without production -j4", "--", \
  "switch", "update=bundle update -j4", "--", \
  "switch", "console=bundle exec rails c", "--", \
  "switch", "rspec=bundle exec rspec", "--", \
  "switch", "rubocop=bundle exec rubocop", "--", \
  "switch", "shell=bash", "--", \
  \
  "prehook", "ruby -v", "--", \
  "prehook", "bundle install --without production -j4 --quiet", "--", \
  "prehook", "rails -v", "--", \
  "prehook", "echo --------", "--" ]
