source "https://rubygems.org"

gem "jekyll", "~> 4.3"

# Windows: tzinfo is no longer a default gem as of Ruby 3.4, and Windows has
# no IANA timezone database of its own, so Jekyll's timezone handling needs
# both gems explicitly here or `jekyll build` fails with a MissingDependencyException.
platforms :windows, :jruby do
  gem "tzinfo", ">= 1", "< 3"
  gem "tzinfo-data"
end
