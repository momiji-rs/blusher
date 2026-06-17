# frozen_string_literal: true

require "bundler/gem_tasks"

# Compile the rb-sys/magnus extension (ext/blusher) into lib/blusher/, against
# the published `carmine` crate. `rake compile` for dev; `gem install` runs the
# same extconf.rb via rake-compiler. Falls back gracefully if rb_sys isn't
# present yet (e.g. before `bundle install`).
begin
  require "rb_sys/extensiontask"

  GEMSPEC = Gem::Specification.load("blusher.gemspec")

  RbSys::ExtensionTask.new("blusher", GEMSPEC) do |ext|
    ext.lib_dir = "lib/blusher"
  end
rescue LoadError
  warn "rb_sys not available; run `bundle install` before `rake compile`"
end

desc "Run rouge's lexer spec suite through blusher (the correctness gate)"
task :spec do
  rouge_src = ENV["ROUGE_SRC"] or abort "set ROUGE_SRC=<rouge checkout with spec/>"
  sh({ "RUBYLIB" => "#{__dir__}/lib" },
     "cd #{rouge_src} && bundle exec ruby -Ilib -Ispec " \
     "-e 'require \"spec_helper\"; require \"blusher\"; " \
     "Dir[\"./spec/lexers/*_spec.rb\"].sort.each { |f| require f }'")
end

# The per-lexer tables (lib/blusher/tables/*.json) are bundled in the gem. To
# regenerate them from a newer rouge, run carmine's tools/extract.rb (in the
# momiji-rs/carmine repo) over the installed rouge — see that repo's tools/.

task default: :compile
