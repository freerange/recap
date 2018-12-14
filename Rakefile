require 'bundler/gem_tasks'
require 'rocco/tasks'
require 'rspec/core/rake_task'

RECAP_DOCS_HOST = ENV['RECAP_DOCS_HOST'] || 'gofreerange.com'

desc 'build docs'
task :doc do
  FileUtils.cd('lib') do
    files = Dir['**/*.rb']
    files.each do |source_file|
      rocco = Rocco.new(source_file, files.to_a, {:stylesheet => "https://#{RECAP_DOCS_HOST}/stylesheets/rocco.css"})
      dest_file = '../docs/' + source_file.sub(Regexp.new("#{File.extname(source_file)}$"), '.html')
      FileUtils.mkdir_p(File.dirname(dest_file))
      File.open(dest_file, 'wb') { |fd| fd.write(rocco.to_html) }
    end
  end
  File.open('docs/index.html', 'w') do |f|
    f.write <<-EOS
      <html><meta http-equiv="refresh" content="0; url=recap.html">
    EOS
  end
end

desc 'publish docs'
task :publish do
  path = "/home/freerange/docs/recap"
  system %{ssh #{RECAP_DOCS_HOST} "sudo rm -fr #{path} && mkdir -p #{path}" && scp -r docs/* #{RECAP_DOCS_HOST}:#{path}}
end

RSpec::Core::RakeTask.new(:spec) do |t|
  t.pattern = "spec/**/*_spec.rb"
  t.rspec_opts = "-fn --color"
end

task :default => :spec
