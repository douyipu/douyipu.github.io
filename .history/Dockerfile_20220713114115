# Create a Jekyll container from a Ruby Alpine image

# At a minimum, use Ruby 2.5 or later
# Uncomment the following line if you want to use GitHub Pages (Jekyll 3.9.x):
FROM ruby:2.7-alpine3.15


# Add Jekyll dependencies to Alpine
RUN apk update
RUN apk add --no-cache build-base gcc cmake git

# Update the Ruby bundler and install Jekyll
RUN gem update bundler && gem install bundler jekyll