---
title: Adding middleware
summary: Add middleware to to extend query execution
---
# Extending the schema

[CHILDREN asList]

## Adding middleware

Middleware is any piece of functionality that is interpolated into
a larger process. A key feature of middleware is that it can be used
with other middlewares in sequence and not have to worry about the order
of execution.

In `silverstripe/graphql`, middleware is used for query execution,
but could ostensibly be used elsewhere too if the API ever accomodates
such an expansion.

[notice]
The middleware API in the `silverstripe/graphql` module is separate from other common middleware
APIs in Silverstripe CMS, such as `HTTPMiddleware`. The two are not interchangable.
[/notice]

The signature for middleware (defined in [`QueryMiddleware`](api:SilverStripe\GraphQL\Middleware\QueryMiddleware)) looks like this:

```php
use GraphQL\Type\Schema;

public abstract function process(Schema $schema, string $query, array $context, array $vars, callable $next);
```

The return value should be [`ExecutionResult`](api:GraphQL\Executor\ExecutionResult) or an `array`.

- `$schema`: The underlying [Schema](https://webonyx.github.io/graphql-php/schema-definition/) object.
  Useful to inspect whether types are defined in a schema.
- `$query`: The raw query string.
- `$context`: An arbitrary array which holds information shared between resolvers.
  Use implementors of [`ContextProvider`](api:SilverStripe\GraphQL\Schema\Interfaces\ContextProvider) to get and set
  data, rather than relying on the array keys directly.
- `$vars`: An array of (optional) [Query Variables](https://graphql.org/learn/queries/#variables).
- `$next`: A callable referring to the next middleware in the chain

Let's write a simple middleware that logs our queries as they come in.

```php
namespace App\GraphQL\Middleware;

use GraphQL\Type\Schema;
use SilverStripe\GraphQL\Middleware\QueryMiddleware;
use SilverStripe\GraphQL\QueryHandler\UserContextProvider;
// ...

class LoggingMiddleware implements QueryMiddleware
{
    public function process(Schema $schema, string $query, array $context, array $vars, callable $next)
    {
        $member = UserContextProvider::get($context);

        Injector::inst()->get(LoggerInterface::class)
            ->info(sprintf(
                'Query executed: %s by %s',
                $query,
                $member ? $member->Title : '<anonymous>';
            ));

        // Hand off execution to the next middleware
        return $next($schema, $query, $context, $vars);
    }
}
```

Now we can register the middleware with our query handler:

```yml
SilverStripe\Core\Injector\Injector:
  SilverStripe\GraphQL\QueryHandler\QueryHandlerInterface.default:
    class: SilverStripe\GraphQL\QueryHandler\QueryHandler
    properties:
      Middlewares:
        logging: '%$App\GraphQL\Middleware\LoggingMiddleware'
```
