<h1>Redmine 3.3.3 for Openshift</h1>
<p> This repository forked from https://github.com/chriswirz/openshift-redmine-3.0.1-quickstart , upgrade to redmine 3.3.3(rails 4.2.1.7, mysql 5.5).
<p>The following procedure will create a redmine instance - and was the procedure used to create this repo after a successful deployment.</p>
<pre>
# The Openshift application (Openshift V2) must be configured as follows:
# Ruby 2.0 (no scaling)
# MySql 5.5

# Begin the installation by navigating to the app runtime directory
cd ~/app-root/runtime/repo/
# download redmine
wget http://www.redmine.org/releases/redmine-3.3.3.tar.gz
tar xvzf redmine-3.3.3.tar.gz
rm redmine-3.3.3.tar.gz

# extract it to its new environment
rm -rf public
rm -rf tmp
mv redmine-3.3.3/* ~/app-root/runtime/repo/
mv redmine-3.3.3/.gitignore ~/app-root/runtime/repo/
rm -rf redmine-3.3.3*

# make sure bundler is installed
gem install bundler --no-ri --no-rdoc --no-document

# get the configuration files
cd ~/app-root/runtime/repo/config
wget —no-check-certificate https://raw.githubusercontent.com/chriswirz/openshift-redmine-3.0.1-quickstart/master/config/database.yml
wget —no-check-certificate https://raw.githubusercontent.com/chriswirz/openshift-redmine-3.0.1-quickstart/master/config/configuration.yml
cd ~/app-root/runtime/repo/

# bundle install
bundle install --no-deployment

# populate the database
rake generate_secret_token
RAILS_ENV=production rake db:migrate
RAILS_ENV=production rake redmine:load_default_data

# restart the app/gear
gear stop
gear start
</pre>

<p>Once the gear is functioning properly, add the contents to the the repository.  You must have the gear's ssh key added to your remote repository's (or application's) allowed key collection.  This example just shows you how to push to your gear's repo, but I have also tested this with github (the repo you're looking at now).</p>
<pre>
cd ~/app-root/runtime/repo/
git init
git add .
git commit -m 'Openshift Quickstart for Redmine 3.3.3'
git remote add origin "ssh://$OPENSHIFT_APP_UUID@$OPENSHIFT_APP_DNS/~/git/ruby.git/"
  replace "ruby.git" to your "openshift_repository.git".
</pre>
<p>Login github, create new repository(openshift-redmine-3.3.3-quickstart).</p>
<pre>
git remote add github "https://github.com/(GITHUB_ID)/openshift-redmine-3.3.3-quickstart.git/"
  replace (GITHUB_ID) to your github id.
git push -u github master
</pre>
