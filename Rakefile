# frozen_string_literal: true

require 'rspec/core/rake_task'

begin
  require 'voxpupuli/rubocop/rake'
rescue LoadError
  # the voxpupuli-rubocop gem is optional
end

namespace :test do
  namespace :spec do
    desc 'Run spec tests'
    RSpec::Core::RakeTask.new(:run) do |t|
      t.rspec_opts = ['--color', '--format documentation']
      t.pattern = 'spec/'
    end
  end

  namespace :acceptance do
    desc 'A quick acceptance test, named because it has no pre-suites to run'
    task :quick do
      ## setup & load_path of beaker's acceptance base and lib directory
      ## see below for the reason why it's commented out atm
      # beaker_gem_spec = Gem::Specification.find_by_name('beaker')
      # beaker_gem_dir = beaker_gem_spec.gem_dir
      # beaker_test_base_dir = File.join(beaker_gem_dir, 'acceptance/tests/base')
      # load_path_option = File.join(beaker_gem_dir, 'acceptance/lib')

      ENV['BEAKER_setfile'] = 'acceptance/config/nodes/hosts.yaml'
      sh('beaker',
         '--hosts', 'acceptance/config/nodes/hosts.yaml',
         ## We can't run these tests until the rsync support in the main
         ## beaker/host.rb is updated to work with passwords.
         # '--tests', beaker_test_base_dir,
         # '--load-path', load_path_option,
         '--tests', 'acceptance/tests/',
         '--log-level', 'debug',
         '--debug')
    end
  end
end

# namespace-named default tasks.
# these are the default tasks invoked when only the namespace is referenced.
# they're needed because `task :default` in those blocks doesn't work as expected.
task 'test:spec': %i[test:spec:run]
task 'test:acceptance': %i[test:acceptance:quick]

# global defaults
task lint: %i[lint:rubocop]
task test: %i[test:spec]
task default: %i[test]

begin
  require 'rubygems'
  require 'github_changelog_generator/task'
rescue LoadError
  # Do nothing if no required gem installed
else
  GitHubChangelogGenerator::RakeTask.new :changelog do |config|
    config.exclude_labels = %w[duplicate question invalid wontfix wont-fix skip-changelog github_actions]
    config.user = 'voxpupuli'
    config.project = 'beaker-docker'
    gem_version = Gem::Specification.load("#{config.project}.gemspec").version
    config.future_release = gem_version
  end
end
