require 'yaml'
require 'fileutils'

desc "Prepare the package"
task :prepare, [:version] do |task,args|
  config = YAML.load_file('config.yml')

  sh "echo #{args.version} > VERSION"

  # prepare directories
  Dir.mkdir 'source' unless Dir.exists?("source")
  FileUtils.mkdir_p 'workdir/etc/init' unless Dir.exists?("workdir/etc/init")
  FileUtils.mkdir_p 'workdir/etc/default' unless Dir.exists?("workdir/etc/default")
  FileUtils.mkdir_p 'workdir/usr/local/bin' unless Dir.exists?("workdir/usr/local/bin")
  FileUtils.mkdir_p 'workdir/usr/share/check_mk/checks' unless Dir.exists?("workdir/usr/share/check_mk/checks")
  FileUtils.mkdir_p 'workdir/usr/lib/nagios/plugins' unless Dir.exists?("workdir/usr/lib/nagios/plugins")
  FileUtils.mkdir_p 'workdir/usr/share/check_mk/web' unless Dir.exists?("workdir/usr/share/check_mk/web")
  Dir.mkdir 'packages' unless Dir.exists?("packages")

  # download and extract mkeventd
  Dir.chdir 'source'
  sh "curl -L #{config['package_url']}/mkeventd-#{args.version}.tar.gz | tar xzf -" unless Dir.exists?("mkeventd-#{args.version}")

  # copy files
  Dir.chdir '..'
  FileUtils.cp_r "source/mkeventd-#{args.version}/bin/.", "workdir/usr/local/bin"
  FileUtils.cp_r "source/mkeventd-#{args.version}/checks/.", "workdir/usr/share/check_mk/checks"
  FileUtils.cp_r "source/mkeventd-#{args.version}/lib/.", "workdir/usr/lib/nagios/plugins"
  FileUtils.cp_r "source/mkeventd-#{args.version}/web/.", "workdir/usr/share/check_mk/web"
  FileUtils.cp "scripts/upstart-job", "workdir/etc/init/mkeventd.conf"
  FileUtils.cp "scripts/defaults", "workdir/etc/default/mkeventd"

end

desc 'Build the package'
task :build do
  config = YAML.load_file('config.yml')

  version = File.read('VERSION').strip
  version += "-#{ENV['BUILD_NUMBER']}" if ENV['BUILD_NUMBER']

  Dir.chdir 'workdir'
  sh "fpm \
-s dir \
-t deb \
-n #{config['package_name']} \
--prefix / \
-v #{version} \
-a all \
--description \"#{config['package_description']}\" \
--url \"#{config['package_website']}\" \
."

  Dir.chdir '..'
  sh "cp workdir/#{config['package_name']}_#{version}_all.deb packages/"

end

desc "Cleanup prepare dirs"
task :clean do
  FileUtils.rm_r 'workdir' if File.exists? 'workdir'
  FileUtils.rm_r 'source' if File.exists? 'source'
  FileUtils.rm_r 'packages' if File.exists? 'packages'
  FileUtils.rm 'VERSION' if File.exists? 'VERSION'
end
