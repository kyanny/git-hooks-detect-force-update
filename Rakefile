require 'tmpdir'

def run(command, options={})
  puts command if options[:verbose]
  system command
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
        run 'git push origin master -q'
      end

      Dir.chdir('test_1') do
        run 'date >> README'
        run 'git add .'
        run 'git commit -m "." -q'
        run 'git push origin master -q'
      end

      run 'git clone test.git test_2 -q'

      Dir.chdir('test_1') do
        2.times do
          run 'date >> README'
          run 'git add .'
          run 'git commit -m "." -q'
          run 'git push origin master -q'
        end

        puts 'IN test_1'
        run 'git log --oneline'
      end

      Dir.chdir('test_2') do
        run 'date >> README'
        run 'git add .'
        run 'git commit -m "."'
        puts 'IN test_2'
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
