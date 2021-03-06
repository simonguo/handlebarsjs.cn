require "bundler/setup"

def git_initialize()
  unless File.exist?(".git")
    system "git init"
    system "git remote add github https://github.com/wycats/handlebars-site.git"
    system "git fetch github"
    system "git checkout -b gh-pages --track github/gh-pages"
  end
end

def git_update
  system "git fetch github"
  system "git reset --hard github/gh-pages"
  # Remove all files so we don't accidentally keep old stuff
  # These will be regenerated by the build process
  system "rm `git ls-files`"
end

def build
  system "staticmatic build ."
  cp_r "src/images", "site/"
  Dir["src/stylesheets/*.css"].each{|f| cp f, "site/stylesheets/" }
end

desc "Build the website"
task :build do
  build
end

task :man do
  # @TODO
end

desc "Deploy the website to github pages"
task :deploy do |t, args|
  require "highline/import"
  message = ask("Provide a deployment message:  ") do |q|
    q.validate = /\w/
    q.responses[:not_valid] = "Can't be empty."
  end

  mkdir_p "site"

  Dir.chdir "site" do
    git_initialize
    git_update
  end

  build

  Dir.chdir "site" do
    File.open("CNAME", 'w') do |f|
      f.write "handlebarsjs.com"
    end

    system "git add -A"
    system "git commit -m '#{message.gsub("'", "\\'")}'"
    system "git push github gh-pages"
  end
end
