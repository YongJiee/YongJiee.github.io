source 'https://rubygems.org'

# Jekyll version
gem 'jekyll', '~> 4.3.0'

# Jekyll plugins
group :jekyll_plugins do
  gem 'jekyll-paginate'
  gem 'jekyll-sitemap'
  gem 'jekyll-seo-tag'
  gem 'jekyll-feed'
  gem 'jekyll-redirect-from'
end

# Performance and optimization
gem 'html-proofer', group: :test
gem 'kramdown-parser-gfm'

# Windows and JRuby compatibility
platforms :mingw, :x64_mingw, :mswin, :jruby do
  gem 'tzinfo', '>= 1', '< 3'
  gem 'tzinfo-data'
end

# Performance booster for watching directories on Windows
gem 'wdm', '~> 0.1', :platforms => [:mingw, :x64_mingw, :mswin]

# Lock `http_parser.rb` gem to `v0.6.x` on JRuby builds
gem 'http_parser.rb', '~> 0.6.0', :platforms => [:jruby]