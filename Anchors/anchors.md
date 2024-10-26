## Anchors

- YAML has a feature called ‘anchors’ that you can use to duplicate content across your document.
- You can’t use YAML anchors across multiple files when using the include keyword. Anchors are only valid in the file they were defined in. To reuse configuration from different YAML files, use !reference tags or the extends keyword.

```yaml
.job_template: &job_configuration  # Hidden yaml configuration that defines an anchor named 'job_configuration'
  image: ruby:2.6
  services:
    - postgres
    - redis

test1: #explain this yml syntax in gitlab 
  <<: *job_configuration           # Add the contents of the 'job_configuration' alias
  script:
    - test1 project

test2:
  <<: *job_configuration           # Add the contents of the 'job_configuration' alias
  script:
    - test2 project
```

- The <<: *job_configuration syntax pulls in all the settings defined under the &job_configuration anchor into each job (test1 and test2).
- << is the merge key operator, and *job_configuration references the previously defined anchor.
- This means test1 and test2 both inherit image: ruby:2.6 and the specified services (Postgres and Redis)

```yaml
.some-script-before: &some-script-before
  - echo "Execute this script first"

.some-script: &some-script
  - echo "Execute this script second"
  - echo "Execute this script too"

.some-script-after: &some-script-after
  - echo "Execute this script last"

job1:
  before_script:
    - *some-script-before
  script:
    - *some-script
    - echo "Execute something, for this job only"
  after_script:
    - *some-script-after
```
In this YAML configuration, the anchors (&some-script-before, &some-script, &some-script-after) are not used with << because they are lists of individual script commands rather than full job configurations or key-value maps.

- Purpose of <<:
The << merge key operator is specifically for merging dictionaries (maps of key-value pairs) into other dictionaries. It’s useful for pulling in all settings from a configuration anchor like job_configuration, which is a dictionary of key-value pairs (e.g., image, services).


- Direct List Referencing with *:
When dealing with lists (like the individual commands under script, before_script, and after_script), YAML allows us to reference these anchors directly without using <<. Here, *some-script-before, *some-script, and *some-script-after refer directly to lists of commands, which are then inserted directly into their respective sections.


- Using Lists vs. Merging Dictionaries:
Lists (as in your example): Can be inserted directly by referencing the anchor with *anchor_name.
Dictionaries (maps): Require << to merge key-value pairs from one dictionary anchor into another.

### Dictionaries vs lists
Example 1: Using << for Dictionaries
When you have a dictionary (a set of key-value pairs) and want to reuse all of its contents in different jobs, you use <<. Here's a simple example:

```yaml
.common-settings: &common-settings  # Defining a dictionary anchor
  image: ruby:2.6
  services:
    - postgres

job1:
  <<: *common-settings  # Using << to include all key-value pairs from common-settings
  script:
    - echo "Job 1 script"

job2:
  <<: *common-settings  # Reusing the same dictionary with <<
  script:
    - echo "Job 2 script"
```
Here image: ruby:2.6, is like a key value pair. So if we use - then we need to use << in the yml template.


```yaml
.common-commands: &common-commands  # Defining a list anchor
  - echo "Step 1"
  - echo "Step 2"

job1:
  before_script:
    - *common-commands  # Reusing the list directly, no <<
  script:
    - echo "Job 1 main script"
  after_script:
    - *common-commands  # Reusing the list directly again

```

here the contents are within - , so its basically like a list of commands or scripts , so we dont have to use 
<< when using the template