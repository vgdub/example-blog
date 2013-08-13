# Standard library
require 'rake'
require 'yaml'
require 'fileutils'

# Load the configuration file
config = YAML.load_file("_config.yml")

# Default Rake
desc "compile and run the site"
task :default do
  pids = [
    spawn("jekyll serve -w"), # put `auto: true` in your _config.yml
    spawn("scss --watch assets/main.scss:css/main.css"),
    spawn("coffee -b -w -o js -c assets/coffee/*.coffee")
  ]

  trap "INT" do
    Process.kill "INT", *pids
    exit 1
  end

  loop do
    sleep 1
  end
end