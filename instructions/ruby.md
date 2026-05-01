# Ruby Library (Gem) Instructions

These instructions apply to Ruby library projects. Read in addition to `core.md`.

---

## Assumptions

- Ruby 3.2+ unless the project specifies otherwise
- No Rails dependency; this is plain Ruby
- Gems are published to [RubyGems.org](https://rubygems.org)
- Testing: RSpec
- Linting: StandardRB (a zero-config wrapper around RuboCop)
- Type checking: Sorbet + Tapioca (see decision:0002; `sorbet-runtime` dev-only, not in gemspec)
- Documentation: YARD

---

## Gem Naming Conventions

- **Single-module gems**: use underscores only — `my_gem` → `MyGem`
- **Namespaced gems**: use hyphens to signal namespace hierarchy — `blacklight-solr` maps
  to the directory structure `lib/blacklight/solr.rb` and module `Blacklight::Solr`
- The hyphen is the conventional separator between the parent namespace and the extension;
  underscores within a segment remain underscores (`blacklight-marc` → `Blacklight::Marc`)

**Agent rule**: When creating or renaming a gem, confirm the naming scheme reflects
the actual module hierarchy. A gem named `foo-bar` should define `Foo::Bar`, not `FooBar`.

---

## Source Layout

```
project-root/
├── lib/
│   ├── my_gem.rb             # Entry point; defines module, requires files
│   └── my_gem/
│       ├── version.rb        # Contains MyGem::VERSION
│       ├── error.rb          # Base error class + specific errors
│       └── ...               # Other classes/modules
│
├── spec/
│   ├── spec_helper.rb        # RSpec config
│   ├── my_gem_spec.rb        # Top-level smoke test
│   ├── my_gem/
│   │   └── ...               # Unit specs mirroring lib/
│   └── integration/
│       └── ...               # Integration specs (require Docker services)
│
├── bin/
│   └── console               # Interactive console for development
│
├── my_gem.gemspec
├── Gemfile
├── Rakefile
└── .standard.yml             # StandardRB config (usually empty; just marks the root)
```

---

## Gemspec

```ruby
# my_gem.gemspec
require_relative "lib/my_gem/version"

Gem::Specification.new do |spec|
  spec.name     = "my_gem"
  spec.version  = MyGem::VERSION
  spec.authors  = ["Your Name"]
  spec.email    = ["you@example.com"]
  spec.summary  = "One-sentence description"
  spec.homepage = "https://github.com/owner/my_gem"
  spec.license  = "MIT"

  spec.required_ruby_version = ">= 3.2"

  spec.metadata = {
    "homepage_uri"    => spec.homepage,
    "source_code_uri" => spec.homepage,
    "changelog_uri"   => "#{spec.homepage}/blob/main/CHANGELOG.md",
    "bug_tracker_uri" => "#{spec.homepage}/issues",
    "rubygems_mfa_required" => "true"  # Require MFA for publishing
  }

  spec.files = Dir["lib/**/*", "LICENSE", "README.md", "CHANGELOG.md"]
  spec.require_paths = ["lib"]

  # Runtime dependencies
  # spec.add_dependency "some_gem", "~> 2.0"

  # Development dependencies go in Gemfile, not gemspec
end
```

**Agent rules**:
- Keep runtime dependencies minimal. Question every `add_dependency`.
- Development tools (`rspec`, `standardrb`, `yard`) go in `Gemfile` under `:development`, not in the gemspec.
- Always set `rubygems_mfa_required` to `"true"` in metadata.

---

## Gemfile

```ruby
# Gemfile
source "https://rubygems.org"

gemspec

group :development, :test do
  gem "rspec", "~> 3.13"
  gem "standardrb", "~> 1.0"
  gem "yard", "~> 0.9"
  gem "rake", "~> 13.0"
end
```

---

## Versioning

Version lives in `lib/my_gem/version.rb`:

```ruby
module MyGem
  VERSION = "1.2.3"
end
```

To release a new version:
1. Update `VERSION` in `lib/my_gem/version.rb`
2. Update `CHANGELOG.md` (move `Unreleased` to `[x.y.z] - YYYY-MM-DD`)
3. Commit: `git commit -m "Release v1.2.3"`
4. Tag: `git tag v1.2.3`
5. Push: `git push && git push --tags`
6. The CI `release.yml` workflow will build and push to RubyGems

Or use the `ruby-gem-release` skill for a guided workflow.

---

## Rake Tasks

Maintain a `Rakefile` with standard tasks:

```ruby
# Rakefile
require "rspec/core/rake_task"
require "rake/clean"
require "yard"

RSpec::Core::RakeTask.new(:spec)
RSpec::Core::RakeTask.new("spec:unit") do |t|
  t.exclude_pattern = "spec/integration/**/*_spec.rb"
end
RSpec::Core::RakeTask.new("spec:integration") do |t|
  t.pattern = "spec/integration/**/*_spec.rb"
end

YARD::Rake::YardocTask.new(:doc)

task default: :spec
```

---

## Linting with StandardRB

```bash
# Check
bundle exec standardrb

# Auto-fix
bundle exec standardrb --fix
```

StandardRB is opinionated and not configurable by design. This is a feature.
Do not fight the style guide. Accept its decisions.

If you genuinely must suppress a rule for a specific line:

```ruby
result = some_thing  # standard:disable Layout/LineLength
```

---

## Error Handling

Define a base error class in `lib/my_gem/error.rb`:

```ruby
module MyGem
  Error = Class.new(StandardError)
  ConfigurationError = Class.new(Error)
  ConnectionError    = Class.new(Error)
  # etc.
end
```

- Never rescue `Exception` (only `StandardError` or subclasses).
- Rescue at the boundary, not in the middle of logic.
- Public API methods should raise your gem's own error classes, not raw underlying errors.
  Wrap third-party exceptions: `rescue Net::TimeoutError => e; raise MyGem::ConnectionError, e.message`.

---

## Interactive Console

```ruby
#!/usr/bin/env ruby
# bin/console
require "bundler/setup"
require "my_gem"
require "irb"
IRB.start(__FILE__)
```

Make executable: `chmod +x bin/console`.

---

## Publishing to RubyGems.org

Prerequisites:
- RubyGems account with MFA enabled
- API key stored as `RUBYGEMS_API_KEY` in GitHub Secrets
- `rubygems_mfa_required: "true"` in gemspec metadata

Manual publish (emergency use only; prefer CI):

```bash
gem build my_gem.gemspec
gem push my_gem-x.y.z.gem
```

---

## Type Checking with Sorbet

Type checking is adopted for this project. See `decision:0002` in `.loom/constitution/decisions/`.

**Toolchain**: Sorbet + Tapioca + `srb tc` in CI.

### Setup

```bash
# Add to Gemfile under :development
gem "sorbet", "~> 0.5"
gem "sorbet-runtime", "~> 0.5"
gem "tapioca", "~> 0.16", require: false

# Initialize Sorbet (creates sorbet/ directory, sorbet/config)
bundle exec srb init

# Generate RBI stubs for gems
bundle exec tapioca gems

# Generate DSL RBIs (RSpec, etc.)
bundle exec tapioca dsl

# Type-check
bundle exec srb tc
```

### Gemspec rule

`sorbet-runtime` is a **development-only** dependency. Do **not** add it to `spec.add_dependency` in the gemspec. It goes in `Gemfile` under `:development` only:

```ruby
# Gemfile
group :development do
  gem "sorbet", "~> 0.5"
  gem "sorbet-runtime", "~> 0.5"
  gem "tapioca", "~> 0.16", require: false
end
```

Rationale: library consumers should not be forced to install Sorbet runtime.

### Annotation style

Use inline `sig` blocks directly above method definitions:

```ruby
# typed: strict
require "sorbet-runtime"

module MyGem
  class Parser
    extend T::Sig

    sig { params(input: String).returns(T::Array[String]) }
    def parse(input)
      input.split(",").map(&:strip)
    end

    sig { params(value: T.nilable(String)).returns(String) }
    def normalize(value)
      value&.strip || ""
    end
  end
end
```

### Strictness levels

| Level | Use when |
|-------|----------|
| `# typed: false` | Legacy files; Sorbet ignores them |
| `# typed: true` | Default for new files |
| `# typed: strict` | Public API files; forces explicit sigs on all methods |
| `# typed: strong` | Optional; disallows `T.untyped` entirely |

Start new files at `# typed: true`. Promote to `# typed: strict` for public API modules.

### CI integration

```yaml
- name: Type check
  run: bundle exec srb tc
```

Add after the lint step in `ci.yml`.

### Agent rules

- Always add `# typed: true` (minimum) to new Ruby files.
- Use `T.nilable(X)` instead of `T.any(X, NilClass)`.
- Use `T::Array[X]`, `T::Hash[K, V]`, `T::Set[X]` for typed collections.
- Run `bundle exec tapioca gems` after adding a new gem dependency.
- When Sorbet cannot resolve a type, add an RBI stub in `sorbet/rbi/shims/` rather than using `T.untyped` everywhere.
- Do not fight the type system by casting to `T.untyped`; treat type errors as signals of bad design.

---

## Ruby Version Support Policy

Support the current stable release and the two previous minor versions.
Update `.ruby-version` and `spec.required_ruby_version` when dropping old versions.
Record the decision in an ADR when dropping a major version.
