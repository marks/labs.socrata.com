require 'yaml'
require 'open-uri'

task :build_apis do
  base_path = File.dirname(__FILE__)
  # Check to make sure you've got aglio installed first
  aglio_bin = `which aglio`.strip
  if !File.executable? aglio_bin
    raise "Error: aglio must be installed and executable by this user"
  end

  # Read in our API config file
  apis = YAML.load(File.read(File.join(base_path, "_data/apis.yml")))

  # Generate an API file for each of the defined APIs
  apis.each do |name, config|
    # Grab its API Blueprint file
    blueprint_file = Tempfile.new(name)
    File.open(blueprint_file, 'w') { |f| f.write(open(config["blueprint"]).read) }

    File.open(File.join(base_path, "docs", name + ".html"), 'w') do |file|
      # Generate our header
      header = <<-eos
---
layout: default
title: #{config["name"]}
description: #{config["description"]}
version: #{config["version"]}
stub: #{name}
type: api
icon: #{config["icon"]}
feedback_url: #{config["feedback_url"]}
last_updated: #{Time.now.strftime("%Y-%m-%d")}
bodyclass: api
custom_css:
- /css/aglio.css
---

<div class="alert alert-info">
  <p><em>Heads up!</em> This is beta version of the {{page.title}}. Functionality may change, so please come back and check this documentation if your code suddenly stops working properly. Feel free to <a href="http://stackoverflow.com/questions/ask?tags=soda,socrata,labs,version-{{page.version}},{{page.stub}}" class="feedback" target="_blank">provide feedback</a> on this API.</p>
</div>
      eos

      # Run it through aglio
      doc = `#{aglio_bin} -i #{blueprint_file.path} -o - -t #{File.join(base_path, "_blueprint/socrata.jade")}`

      # Write the sucker!
      $stderr.puts "Writing docs for #{name}"
      file.puts header
      file.puts doc
    end
  end
end
