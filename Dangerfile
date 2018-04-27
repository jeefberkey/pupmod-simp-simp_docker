# Dangerfile!
# vi:syntax=ruby

#Danger::Changelog.configure do |config|
# config.filename = 'CHANGELOG'
#end
#changelog.have_you_updated_changelog?
#changelog.is_changelog_format_correct?


declared_trivial = github.pr_title.include? '#trivial'

has_puppet_changes = !git.modified_files.grep(/.pp$/).empty?
has_hiera_changes = !git.modified_files.grep(/^hieradata\/.yaml$|^data\/.yaml$|.pp$/).empty?
has_spec_changes = !git.modified_files.grep(/spec/).empty?
has_acceptance_changes = !git.modified_files.grep(/spec\/acceptance\/suites/).empty?
is_wip = github.pr_title.include? 'WIP'

warn('Be sure to run the acceptance tests!') if has_acceptance_changes

if has_puppet_changes && !has_spec_changes
  warn('There are changes in manifests, but not tests. That\'s OK as long as you\'re refactoring existing code.', sticky: false)
end

warn('PR is classed as Work in Progress') if is_wip

warn('Big PR') if git.lines_of_code > 500

if github.pr_body.length < 5
  warn 'Please provide a summary in the Pull Request description'
end

if !git.modified_files.include?('CHANGELOG') && has_puppet_changes
  warn('Please include a CHANGELOG entry when changing version).')
end


string_reference = `puppet strings generate --format markdown`
if ! string_reference.include? '100.00% documented'
  fail('Parts of the code are not documented! See the output of `puppet strings generate --format markdown`')
elsif string_reference.include? 'warning'
  warn('There are some warnings from puppet strings! See the output of `puppet strings generate --format markdown`')
end

if system('git diff --exit-code REFERENCE.md')
  fail('Run `puppet strings generate --format markdown` and update the REFERENCE.md')
end

unless github.api.organization_member?('simp', github.pr_author)
  message(':tada: Thanks for your contribution!')
end


if github.pr_title !~ /^\(SIMP-\d{3,5}\)/ or is_wip
  fail('Please follow our [Commit Message Guidelines](http://simp.readthedocs.io/en/master/contributors_guide/Contribution_Procedure.html#commit-message-conventions)')
end

# We can totally do linting in here, like yaml validating and rpm stuff.

