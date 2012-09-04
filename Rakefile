require 'tmpdir'
require 'securerandom'

def run(command, options={})
  puts command if options[:verbose]
  system command
end

def write
  open('README', 'a'){ |f| f.puts SecureRandom.uuid }
end

def commit
  run 'git add .'
  run 'git commit -m "." -q'
end

def commit0
  run 'git commit --allow-empty -m "Initial commit" -q'
end

def push
  run 'git push origin master -q'
end

def branch(branch_name)
  run "git branch #{branch_name}"
end

def tag(tag_name)
  run "git tag #{tag_name}"
end

def clone(repo_name)
  run "git clone test.git #{repo_name} -q"
end

def install_hook(hook_name)
  run "cp #{$pwd}/#{hook_name} test.git/hooks/#{hook_name}"
  run "chmod a+x test.git/hooks/#{hook_name}"
end

desc 'git hooks test detect force update'
task :default do
  $pwd = Dir.getwd

  Dir.mktmpdir do |tmp|
    Dir.chdir(tmp) do
      run 'git init --bare test.git -q'

      clone('test1')
      Dir.chdir('test1') do
        commit0
        write; commit; tag('B'); push # HEAD is 'B'
      end

      clone('test2') # HEAD is 'B'

      Dir.chdir('test1') do
        write; commit; write; commit; tag('O'); push # HEAD is 'O'
      end

      install_hook('pre-receive')

      Dir.chdir('test2') do
        write; commit; write; commit; tag('X')
        write; commit; write; commit; tag('N') # HEAD is 'N'

        run 'git push -f origin master'
      end
    end
  end
end
