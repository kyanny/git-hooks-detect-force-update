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

task :do_task do
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
        if $is_forced_update
          write; commit; write; commit; tag('O'); push # HEAD is 'O'
        end
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

desc 'git hooks test'
task :normal_push do
  $is_forced_update = false
  do_task
end

desc 'git hooks test detect forced update'
task :forced_push do
  $is_forced_update = true
  do_task
end
