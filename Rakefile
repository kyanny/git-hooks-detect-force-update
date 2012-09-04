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

def push
  run 'git push origin master -q'
end

def branch(branch_name)
  run "git branch #{branch_name}"
end

desc 'git hooks test detect force update'
task :default do
  pwd = Dir.getwd
  Dir.mktmpdir do |tmp|
    Dir.chdir(tmp) do
      run 'git init --bare test.git -q'

      run 'git clone test.git test_1 -q'
      Dir.chdir('test_1') do
        run 'git commit --allow-empty -m "Initial commit" -q'
        push
      end

      Dir.chdir('test_1') do
        write; commit; push;
        write; commit; push;
      end

      run 'git clone test.git test_2 -q'

      Dir.chdir('test_1') do
        2.times do
          write; commit; push;
          write; commit; push;
        end

        puts '--> IN test_1'
        run 'git log --oneline'
      end

      Dir.chdir('test_2') do
        branch('foo'); branch('bar'); branch('baz');
      end

      Dir.chdir('test_2') do
        write; commit; push;
        write; commit; push;

        puts '--> IN test_2'
        run 'git log --oneline'
      end

      run "cp #{pwd}/pre-receive test.git/hooks/pre-receive"
      run 'chmod a+x test.git/hooks/pre-receive'

      puts '-' * 80
      Dir.chdir('test_2') do
        run 'git push origin master', verbose: true
      end

      puts '-' * 80
      Dir.chdir('test_2') do
        run 'git push -f origin master', verbose: true
      end
    end
  end
end
