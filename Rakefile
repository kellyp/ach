require 'rake'
require 'rake/testtask'
require 'rake/rdoctask'

require 'rubygems'
require 'rake/gempackagetask'
require 'spec/rake/spectask'

begin
  require 'jeweler'
  Jeweler::Tasks.new do |gem|
    gem.name = "ach"
    gem.summary = %{Helper for building ACH files in Ruby}
    gem.description = <<EOF
ach is a Ruby helper for builder ACH files. In particular, it helps with field
order and alignment, and adds padding lines to end of file.
EOF
    gem.email = "jmorgan@morgancreative.net"
    gem.homepage = "http://github.com/jm81/ach"
    gem.authors = ["Jared Morgan"]
    # gem is a Gem::Specification... see http://www.rubygems.org/read/chapter/20 for additional settings
  end
  Jeweler::GemcutterTasks.new
rescue LoadError
  puts "Jeweler (or a dependency) not available. Install it with: sudo gem install jeweler"
end

require 'micronaut/rake_task'
Micronaut::RakeTask.new(:examples) do |examples|
  examples.pattern = 'examples/**/*_example.rb'
  examples.ruby_opts << '-Ilib -Iexamples'
end

Micronaut::RakeTask.new(:rcov) do |examples|
  examples.pattern = 'examples/**/*_example.rb'
  examples.rcov_opts = '-Ilib -Iexamples'
  examples.rcov = true
end

task :default => :examples

require 'rake/rdoctask'
Rake::RDocTask.new do |rdoc|
  version = File.exist?('VERSION') ? File.read('VERSION') : ''
  rdoc.rdoc_dir = 'rdoc'
  rdoc.title = "ACH #{version}"
  rdoc.rdoc_files.include('README*')
  rdoc.rdoc_files.include('lib/**/*.rb')
end

# Add your own tasks in files placed in lib/tasks ending in .rake,
# for example lib/tasks/capistrano.rake, and they will automatically be available to Rake.


require File.join(File.dirname(__FILE__), 'version')

module PennyMac
  module ACH
    def self.package_files(package_prefix)
      FileList[
        "#{package_prefix}bin/**/*",
        "#{package_prefix}lib/**/*",
      ] - [ "#{package_prefix}spec" ]
    end
  
    def self.gem_spec(package_prefix = '')
      eval(File.read("#{File.dirname(__FILE__)}/ach.gemspec"))
    end
  end
end

desc "Default task"
task :default => :verify_rcov 

desc "Verify specs and check coverage"
task :verify_rcov => [:spec]

desc "Building"
task :build => [:clean, :depends, :gem, :install, :verify_rcov]

desc "update"
task :update => [:build]

desc "install"
task :install do
  %x[gem install pkg/"#{PennyMac::ACH::Package::PACKAGE_FILE_NAME}.gem"]
end

desc "Run all specs"
Spec::Rake::SpecTask.new do |t|
  t.spec_files = FileList['spec/**/*_spec.rb']
  t.spec_opts = ['--options', 'spec/spec.opts']
  unless ENV['NO_RCOV']
    t.rcov = true
    t.rcov_dir = 'coverage'
    t.rcov_opts = ['--text-report', '--exclude', "teamcity,lib/spec.rb,lib/spec/runner.rb,spec\/spec,bin\/spec,examples,\/gems,\/Library\/Ruby,\.autotest,#{ENV['GEM_HOME']}"]
  end
end

desc "Installing dependencies"
task :depends do 
  PennyMac::ACH.gem_spec.dependencies.each do |dep|
    begin
      gem dep.name
    rescue Gem::LoadError
      %x[gem install #{dep.name}]
    end
  end
end

Rake::GemPackageTask.new(PennyMac::ACH.gem_spec) do |pkg| 
  pkg.gem_spec = PennyMac::ACH.gem_spec
  pkg.need_zip = true
  pkg.need_tar = true 
end 

desc "Cleaning up generated directories and files"
task :clean do
  rm_rf "pkg"
  rm_rf "doc"
  rm_rf "coverage"
  rm_rf "log"
  rm_rf "tmp"
end

