# A couple of Rake tasks inspired by Octopress to make blogging with
# Jekyll a bit easier.
def get_stdin(message)
  print message
  STDIN.gets.chomp
end

# TODO: Replace this with something proper
# I guess I should find what exactly was used by Octopress
def slug(title)
  title.downcase.gsub(/\s+/, '-')
end

# usage rake new_post[my-new-post] or rake new_post['my new post'] or rake new_post
desc 'Create a new post in _posts/'
task :new_post, :title do |_t, args|
  title = args.title || get_stdin('Enter a title for your post: ')
  filename = "_posts/#{Time.now.strftime('%Y-%m-%d')}-#{slug(title)}.md"

  if File.exist?(filename)
    abort('rake aborted!') if ask("#{filename} already exists. Do you want to overwrite?", %w[y n]) == 'n'
  end

  puts "Creating new post: #{filename}"

  File.open(filename, 'w') do |post|
    post.puts '---'
    post.puts 'layout: post'
    post.puts "title: \"#{title.gsub(/&/, '&amp;')}\""
    post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M:%S %z')}"
    post.puts 'categories: posts'
    post.puts 'tags:'
    post.puts '---'
  end
end

# usage rake new_page[my-new-page] or rake new_page
desc 'Create a new page'
task :new_page, :title do |_t, args|
  title = args.title || get_stdin('Enter a title for your page: ')

  filename = "#{slug(title)}.md"

  if File.exist?(filename)
    abort('rake aborted!') if ask("#{filename} already exists. Do you want to overwrite?", %w[y n]) == 'n'
  end

  puts "Creating new page: #{filename}"

  File.open(filename, 'w') do |page|
    page.puts '---'
    page.puts 'layout: page'
    page.puts "title: \"#{title}\""
    page.puts "permalink: /#{slug(title)}/"
    page.puts '---'
  end
end
