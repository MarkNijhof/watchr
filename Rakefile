# --------------------------------------------------
# based on thin's Rakefile (http://github.com/macournoyer/thin)
# --------------------------------------------------
require 'rake/gempackagetask'
require 'rake/rdoctask'
require 'pathname'
require 'yaml'
require 'lib/watchr/version'
begin
  require 'yard'
rescue LoadError, RuntimeError
end

RUBY_1_9  = RUBY_VERSION =~ /^1\.9/
WIN       = (RUBY_PLATFORM =~ /mswin|cygwin/)
SUDO      = (WIN ? "" : "sudo")

def gem
  RUBY_1_9 ? 'gem19' : 'gem'
end

def all_except(res)
  Dir['**/*'].reject do |path|
    Array(res).any? {|re| path.match(re) }
  end
end

spec = Gem::Specification.new do |s|
  s.name            = 'watchr'
  s.version         =  Watchr.version
  s.summary         = "Continious anything"
  s.description     = "Continious anything; project files observer/trigger."
  s.author          = "Martin Aumont"
  s.email           = 'mynyml@gmail.com'
  s.homepage        = ''
  s.has_rdoc        = true
  s.require_path    = "lib"
  s.bindir          = "bin"
  s.executables     = "watchr"
  s.files           = all_except %w( ^doc ^pkg ^test/fixtures )
 #s.add_dependency 'every', '>= 1.0'
  s.add_dependency 'rev',   '>= 0.3.0'
end

desc "Generate rdoc documentation."
Rake::RDocTask.new(:rdoc => 'rdoc', :clobber_rdoc => 'rdoc:clean', :rerdoc => 'rdoc:force') { |rdoc|
  rdoc.rdoc_dir = 'doc/rdoc'
  rdoc.title    = "Watchr"
  rdoc.options << '--line-numbers' << '--inline-source'
  rdoc.options << '--charset' << 'utf-8'
  rdoc.main = 'README.rdoc'
  rdoc.rdoc_files.include('README.rdoc')
  rdoc.rdoc_files.include('TODO.txt')
  rdoc.rdoc_files.include('LICENSE')
  rdoc.rdoc_files.include('lib/**/*.rb')
}

if defined? YARD
  YARD::Rake::YardocTask.new do |t|
    t.files   = %w( lib/**/*.rb )
    t.options = %w( -o doc/yard --readme README.rdoc --files LICENSE,TODO.txt )
  end
end

Rake::GemPackageTask.new(spec) do |p|
  p.gem_spec = spec
end

desc "Remove package products"
task :clean => :clobber_package

desc "Update the gemspec for GitHub's gem server"
task :gemspec do
  Pathname("#{spec.name}.gemspec").open('w') {|f| f << YAML.dump(spec) }
end

desc "Install gem"
task :install => [:clobber, :package] do
  sh "#{SUDO} #{gem} install pkg/#{spec.full_name}.gem"
end

desc "Uninstall gem"
task :uninstall => :clean do
  sh "#{SUDO} #{gem} uninstall -v #{spec.version} -x #{spec.name}"
end
