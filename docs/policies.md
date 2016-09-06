# Policies

Policies provide the ability to modify the behavior of operations.

This document provides information on the structure of policy objects for specific
operations and the allowed values for some of the policies.

- [`Policy Objects`](#Objects)
- [`Policy Values`](#Values)


<a name="Objects"></a>
## Objects

Policy objects are classes which define the behavior of associated operations.

When invoking an operation, you can choose:

- pass `nil` as policy. A relevant default policy will be used.
- pass policy in method options; e.g. `:ttl => 1`
- Generate the relevant policy object directly and pass it instead of the options parameter.

Usage Example:

```ruby
  client.get(key);
  client.get(key, [], policy.new);
```

<!--
################################################################################
Policy
################################################################################
-->
<a name="Policy"></a>

### Policy Object

A policy effecting the behaviour of read operations.

Attributes:

- `priority`                – Specifies the behavior for the key.
                            For values, see [Priority Values](policies.md#priority).
                            * Default: `Priority.DEFAULT`
- `timeout`                 – Maximum time to wait for
                            the operation to complete. If 0 (zero), then the value
                            means there will be no timeout enforced.
                            Value should be in seconds.
                            * Default: `0 * time.Milliseconds` (no timeout)
- `max_retries`             – Number of times to try on connection errors.
                            * Default: `2`
- `sleep_between_retries`   – Duration of waiting between retries.
                            * Default: `0.500` (500ms)


<!--
################################################################################
WritePolicy
################################################################################
-->
<a name="WritePolicy"></a>

### WritePolicy Object

A policy effecting the behaviour of write operations.

Includes All Policy attributes, plus:

- `record_exists_action`   – Qualify how to handle writes where the record already exists.
                           For values, see [RecordExistsAction Values](policies.md#exists).
                           * Default: `RecordExistsAction.UPDATE`
- `generation_policy`      – Qualify how to handle record writes based on record generation.
                           For values, see [GenerationPolicy Values](policies.md#gen).
                           * Default: `GenerationPolicy.NONE` (generation is not used to restrict writes)
- `generation`             – Expected generation. Generation is the number of times a record has been modified
                           (including creation) on the server. If a write operation is creating a record,
                           the expected generation would be 0
                           * Default: `0`
- `expiration`             – Record expiration. Also known as ttl (time to live). Seconds record will live before being removed by the server.
                           Expiration values:
                           * -1: Never expire for Aerospike 2 server versions >= 2.7.2 and Aerospike 3 server versions >= 3.1.4. 
                           Do not use -1 for older servers.
                           * 0: Default to namespace configuration variable "default-ttl" on the server.
                           * > 0: Actual expiration in seconds.
                           * Default: `0`


<a name="Values"></a>
## Values

The following are values allowed for various policies.

<!--
################################################################################
gen
################################################################################
-->
<a name="gen"></a>

### GenerationPolicy Values

- **NONE**

Writes a record, regardless of generation.

- **EXPECT_GEN_EQUAL**

Writes a record, ONLY if generations are equal.

- **EXPECT_GEN_GT**

Writes a record, ONLY if local generation is greater-than remote generation.

- **DUPLICATE**

Writes a record creating a duplicate, ONLY if the generation collides.

<!--
################################################################################
exists
################################################################################
-->
<a name="exists"></a>

### RecordExistsAction Values

- **UPDATE**

  Create or update record.

  Merge write command bins with existing bins.

- **UPDATE_ONLY**

  Update record only. Fail if record does not exist.

  Merge write command bins with existing bins.

- **REPLACE**

  Create or replace record.

  Delete existing bins not referenced by write command bins.

  Supported by Aerospike 2 server versions >= 2.7.5 and

  Aerospike 3 server versions >= 3.1.6.

- **REPLACE_ONLY**

  Replace record only. Fail if record does not exist.

  Delete existing bins not referenced by write command bins.

  Supported by Aerospike 2 server versions >= 2.7.5 and

  Aerospike 3 server versions >= 3.1.6.

- **CREATE_ONLY**

  Create only.  Fail if record exists.


<!--
################################################################################
priority
################################################################################
-->
<a name="priority"></a>

### Priority Values

- **DEFAULT**

  The server defines the priority.

- **LOW**

  Run the database operation in a background thread.

- **MEDIUM**

  Run the database operation at medium priority.

- **HIGH**

  Run the database operation at the highest priority.
  