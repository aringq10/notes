# DI, container
## Autowiring, attributes, .yml config files
The Kernel component compiles the optimized .php container in var/cache/<env>. It parses .yml config files, walks all classes it can reach, uses reflection on them to detect attributes like #[Autowire] or event listeners. Autowiring works by compiling get<ServiceName> classes for each service and passing concrete classes as ctor args (resolved at compile time). It keeps a central $container, which caches already created service objects for reuse. Resolving interface -> implementation comes from .yml config.

## Symfony bundles
config/bundles.php tells symfony which bundles to load.
There are php libraries (pure logic), and then there are bundles which wrap them and integrate them into symfony at container build time. Symfony finds all Extension classes (from config/bundles.php) and calls their load() method, in which the bundle registers its services with symfony. Under the hood it also uses its Configuration class to declare how symfony should parse its .yml config file, upon which symfony returns the passed config; the bundle then uses the config to further customize the services it registers. A bundle also registers compiler passes for collecting services with common tags (a key -> closure map); it can then get this list from a Locator class passed as an arg and autowired like any other class.

src/ is autowired, not like bundles in vendor/.

# Request lifecycle
A request is split up into these events, all of which can be hooked on and intercepted by other services:
- kernel.request - before anything
- kernel.controller - controller is known but not yet called
- kernel.view - only if the controller returned a non-Response
- kernel.response - Response exists, about to be sent
- kernel.terminate - after the response is sent to the client
- kernel.exception - an error occurs

# Routing
An compiled optimized regex lookup table routing url paths to controller service methods.
