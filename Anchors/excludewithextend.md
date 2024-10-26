## Exclude a key from extends

```yaml
.base:
  script: test
  variables:
    VAR1: base var 1

test1:
  extends: .base
  variables:
    VAR1: test1 var 1
    VAR2: test2 var 2

test2:
  extends: .base
  variables:
    VAR2: test2 var 2

test3:
  extends: .base
  variables: {}

test4:
  extends: .base
  variables: null

```

### Base Job (.base):
```yaml
.base:
  script: test
  variables:
    VAR1: base var 1

```
This base job defines a script and a variables section with one variable, VAR1.
Other jobs (test1, test2, etc.) will extend this base job.

### Job test1:
```yaml
test1:
  extends: .base
  variables:
    VAR1: test1 var 1
    VAR2: test2 var 2
```

- Extends: .base.
- Overrides VAR1 by changing its value to "test1 var 1".
- Adds a new variable, VAR2, with the value "test2 var 2"
- Resulting variables for test1
```yaml
VAR1: test1 var 1
VAR2: test2 var 2

```