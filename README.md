# Jekyll Doc Theme

Go to [the website](https://aksakalli.github.io/jekyll-doc-theme/) for detailed information and demo.

## Running locally

You need Ruby and gem before starting, then:

```bash
# install bundler
gem install bundler

# clone the project
git clone https://github.com/aksakalli/jekyll-doc-theme.git
cd jekyll-doc-theme

# run jekyll with dependencies
bundle exec jekyll serve
```

### Install Ruby
    
**pre-requirements**

```bash
# install dependency 
sudo apt-get install autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm3 libgdbm-dev
```
    
**install rbenv**

```bash
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
source ~/.bashrc 
```
    
**how to check installing rbenv**

```bash
rbenv
    
>>
rbenv 1.1.1-28-gb943955
Usage: rbenv <command> [<args>]

Some useful rbenv commands are:
   commands    List all available rbenv commands
   local       Set or show the local application-specific Ruby version
   global      Set or show the global Ruby version
   shell       Set or show the shell-specific Ruby version
   rehash      Rehash rbenv shims (run this after installing executables)
   version     Show the current Ruby version and its origin
   versions    List all Ruby versions available to rbenv
   which       Display the full path to an executable
   whence      List all Ruby versions that contain the given executable

See `rbenv help <command>' for information on a specific command.
For full documentation, see: https://github.com/rbenv/rbenv#readme
```
    
**install ruby-build plugin**

```bash
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
```
    
**how to check installing ruby-build plugin**
had been added `install` and `uninstall` in rbenv command output
```bash
rbenv
    
>>
rbenv 1.1.1-28-gb943955
Usage: rbenv <command> [<args>]

Some useful rbenv commands are:
   commands    List all available rbenv commands
   local       Set or show the local application-specific Ruby version
   global      Set or show the global Ruby version
   shell       Set or show the shell-specific Ruby version
   *install     Install a Ruby version using ruby-build
   *uninstall   Uninstall a specific Ruby version
   rehash      Rehash rbenv shims (run this after installing executables)
   version     Show the current Ruby version and its origin
   versions    List all Ruby versions available to rbenv
   which       Display the full path to an executable
   whence      List all Ruby versions that contain the given executable

See `rbenv help <command>' for information on a specific command.
For full documentation, see: https://github.com/rbenv/rbenv#readme
```
    
**install ruby**
```bash
rbenv install -l (소문자 L)
rbenv install 2.5.0
rbenv global 2.5.0
ruby -v
```
    
**install Gem**
```bash
gem install bundler
gem install github-pages
gem install jekyll
bundle install
bundle update
```
        
**Run**

input command in jekyll re

```bash
bundle exec jekyll serve
```
    
## License

Released under [the MIT license](LICENSE).
