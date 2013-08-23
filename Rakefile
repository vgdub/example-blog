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

# rake post["Post title"]
desc "Create a post in _posts"
task :post, :title do |t, args|
  title     = args[:title]
  template  = config["post"]["template"]
  extension = config["post"]["extension"]
  editor    = config["editor"]

  if title.nil? or title.empty?
    raise "Please add a title to your post."
  end

  date     = Time.now.strftime("%Y-%m-%d")
  filename = "#{date}-#{title.gsub(/(\'|\!|\?|\:|\s\z)/,"").gsub(/\s/,"-").downcase}.#{extension}"
  content  = File.read(template)

  if File.exists?("_posts/#{filename}")
    raise "The post already exists."
  else
    parsed_content = "#{content.sub("title:", "title: \"#{title}\"", ).sub("date:", "date: \"#{date}\"")}"
    File.write("_posts/#{filename}", parsed_content)
    puts "#{filename} was created."

    if editor && !editor.nil?
      sleep 2
      system "#{editor} _posts/#{filename}"
    end
  end
end

# rake draft["Post title"]
desc "Create a post in _drafts"
task :draft, :title do |t, args|
  title     = args[:title]
  template  = config["post"]["template"]
  extension = config["post"]["extension"]
  editor    = config["editor"]

  if title.nil? or title.empty?
    raise "Please add a title to your post."
  end

  filename = "#{title.gsub(/(\'|\!|\?|\:|\s\z)/,"").gsub(/\s/,"-").downcase}.#{extension}"
  content  = File.read(template)

  if File.exists?("_drafts/#{filename}")
    raise "The post already exists."
  else
    parsed_content = "#{content.sub("title:", "title: \"#{title}\"")}"
    File.write("_drafts/#{filename}", parsed_content)
    puts "#{filename} was created."

    if editor && !editor.nil?
      sleep 2
      system "#{editor} _drafts/#{filename}"
    end
  end
end

# rake publish
# rake publish["post-title"]
desc "Move a post from _drafts to _posts"
task :publish, :post do |t, args|
  post      = args[:post]
  extension = config["post"]["extension"]

  if post.nil? or post.empty?
    Dir["_drafts/*.#{extension}"].each do |filename|
      list = File.basename(filename, ".*")
      puts list
    end
  else
    date     = Time.now.strftime("%Y-%m-%d")
    filename = "#{post}.#{extension}"

    FileUtils.mv("_drafts/#{filename}", "_posts/#{date}-#{filename}")
    puts "#{filename} was moved to _posts."
  end
end

# rake page["Page title"]
# rake page["Page title","Path/to/folder"]
desc "Create a page (with an optional filepath)"
task :page, :title, :path do |t, args|
  title     = args[:title]
  filepath  = args[:path]
  template  = config["page"]["template"]
  extension = config["page"]["extension"]
  editor    = config["editor"]

  if title.nil? or title.empty?
    raise "Please add a title to your page."
  end

  if filepath.nil? or filepath.empty?
    filepath = "./"
  else
    FileUtils.mkdir_p("#{filepath}")
  end

  filename = "#{title.gsub(/(\'|\!|\?|\:|\s\z)/,"").gsub(/\s/,"-").downcase}.#{extension}"
  content  = File.read(template)

  if File.exists?("#{filepath}/#{filename}")
    raise "The page aldready exists."
  else
    parsed_content = "#{content.sub("title:", "title: \"#{title}\"")}"
    File.write("#{filepath}/#{filename}", parsed_content)
    puts "#{filename} was created in #{filepath}."

    if editor && !editor.nil?
      sleep 2
      system "#{editor} #{filepath}/#{filename}"
    end
  end
end