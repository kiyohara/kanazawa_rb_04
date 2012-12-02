require "rake/clean";

DIR_SRC = "src";
DIR_DIST = "dist";

dist_entries = [ DIR_DIST ];

directory DIR_DIST

Dir.glob(DIR_SRC + "/**/*") do |src_entry|
  dist_entry = src_entry.sub(/^#{DIR_SRC}/, "#{DIR_DIST}");
  dist_dirname = File.dirname(dist_entry);

  if File.directory?(src_entry) then
    directory dist_entry;
  else
    ext_name = File.extname(src_entry);

    if /\.(js)$/i =~ ext_name then
      file dist_entry => [src_entry, dist_dirname] do |t|
        compress_js(src_entry, dist_entry);
      end
    elsif /\.(css)$/i =~ ext_name then
      file dist_entry => [src_entry, dist_dirname] do |t|
        compress_css(src_entry, dist_entry);
      end
    elsif /\.(coffee)$/i =~ ext_name then
      dist_js = Pathname(dist_entry).sub_ext(".js").to_s;
      dist_min_js = Pathname(dist_entry).sub_ext(".min.js").to_s;

      file dist_js => [src_entry, dist_dirname] do |t|
        compile_coffee(src_entry, dist_js);
      end

      file dist_min_js => [dist_js] do |t|
        compress_js(dist_js, dist_min_js);
      end

      dist_entry = dist_min_js;
    elsif /\.(less)$/i =~ ext_name then
      dist_css = Pathname(dist_entry).sub_ext(".css").to_s;
      dist_min_css = Pathname(dist_entry).sub_ext(".min.css").to_s;

      file dist_css => [src_entry, dist_dirname] do |t|
        compile_less(src_entry, dist_css);
      end

      file dist_min_css => [dist_css] do |t|
        compress_css(dist_css, dist_min_css);
      end

      dist_entry = dist_min_css;
    elsif /\.(html)$/i =~ ext_name then
      file dist_entry => [src_entry, dist_dirname] do |t|
        compress_html(src_entry, dist_entry);
      end
    elsif /\.(png|jpg)$/i =~ ext_name then
      file dist_entry => [src_entry, dist_dirname] do |t|
        compress_image(src_entry, dist_entry);
      end
    else
      file dist_entry => [src_entry, dist_dirname] do |t|
        copy(t.prerequisites[0], t.name);
        # t.prerequisites[0] = src_entry
        # t.name             = dist_entry
      end
    end
  end

  dist_entries.push(dist_entry);
end

CLEAN.include(dist_entries);

desc "generate dist files"
task :make_dist => dist_entries;

task :default => [:make_dist]

def compress_html(src, dist)
  puts "compress_html: #{src} #{dist}";
  res = `tidy -config tidy.conf -output #{dist} #{src}`; #XXX
end

def compile_coffee(src, dist)
  puts "compile_coffee: #{src} #{dist}";
  res = `coffee -p -c #{src} > #{dist}`; #XXX
end

def compress_js(src, dist)
  puts "compress_js: #{src} #{dist}";
  res = `yuicompressor #{src} > #{dist}`; #XXX
end

def compile_less(src, dist)
  puts "compile_less: #{src} #{dist}";
  res = `lessc #{src} > #{dist}`; #XXX
end

def compress_css(src, dist)
  puts "compress_css: #{src} #{dist}";
  res = `yuicompressor #{src} > #{dist}`; #XXX
end

def compress_image(src, dist)
  puts "compress_image: #{src} #{dist}";
  copy(src, dist); #XXX :p
end

