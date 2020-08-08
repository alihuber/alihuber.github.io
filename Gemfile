source "https://rubygems.org"

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem "jekyll", versions['jekyll']
gem "kramdown", versions['kramdown']
gem "kramdown-parser-gfm", versions['kramdown-parser-gfm']
gem "jekyll-sitemap", versions['jekyll-sitemap']
