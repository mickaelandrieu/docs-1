# The BackBee documentation

## How to contribute ?

The documentation is written in markdown and we use mkdocs to generate the website.
In order to contribute, you need to install python, python-yaml and mkdocs.

Then, use this command:

```bash
$ mkdocs serve
```

You will have the documentation website available in your own computer.

### Register a new page

You need to create the new ``<page>.md`` file and then to register it into ``mkdocs.yml`` configuration file,
in ``pages`` section.

For instance:

```yaml
# mkdocs.yml
pages:
    - Introduction: 'index.md'
    - 'Developer documentation':
        - 'Requirements': 'developer-documentation/requirements.md'
        - 'Architecture': 'developer-documentation/architecture.md'
        - 'Configuration': 'developer-documentation/configuration.md'
        - 'Project Architecture': 'developer-documentation/project_architecture.md'
        - **'New page': 'new_page.md'**
    - 'Cookbooks':
        - 'Create new commands': 'developer-documentation/cookbooks/create_new_commands.md'
        - 'Customize error pages': 'developer-documentation/cookbooks/customize_error_pages.md'
        - 'Use Swift Mailer in BackBee': 'developer-documentation/cookbooks/use_swift_mailer_in_backbee.md'
        - 'Create bundle administration': 'developer-documentation/cookbooks/create_bundle_administration.md'
        - 'Layout creation using LayoutBuilderBundle': 'developer-documentation/cookbooks/layout_creation.md'
    - 'Components':
        - 'ClassContent': 'developer-documentation/components/classcontent.md'
        - 'Config': 'developer-documentation/components/config.md'
        - 'Event Listener': 'developer-documentation/components/event_listener.md'
        - 'Routing': 'developer-documentation/components/routing.md'
        - 'Security': 'developer-documentation/components/security.md'
        - 'Translation': 'developer-documentation/components/translation.md'
        - 'Nested Node': 'developer-documentation/components/nested_node.md'
```

**Mkdocs** can manage only 1 level of subpage, this configuration:

```yaml
pages:
    - 'foo': 'foo.md'
        - 'bar': 'foo/bar.md'
            - 'baz': 'baz.md'
```

will result to an error, and the website won't be generated.

## Deploy the documentation (maintainers only)

Use this command *after each pull request merged*:

```bash
$ mkdocs gh-deploy
```
