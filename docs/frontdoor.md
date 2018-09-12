# Registry Frontdoor
> https://github.com/npm/registry-frontdoor

Registry Frontdoor is the interface for authentication and authorization for
all of npm's services. From logging in via the CLI, to adding collaborators
to a package via the website, Regsitry Frontdoor is the service that
confirms that you are you and that you have permission to perform the 
actions you request.

## Architecture

Registry Frontdoor is a [`node-restify`] Application that is built on top
of a [CouchDB] database.

It's worth noting that many applications using CouchDB leverage the 
platform's ability to build an application within the database layer.
At one point, this service did that, but is in the process of transitioning
away from that structure. (Here be some complexity, see below for more
details.)

[`node-restify`]: https://github.com/restify/node-restify
[CouchDB]: http://couchdb.apache.org/

## Routes

The routes that are exposed by this service live in the [`/routes`]
directory.

[`/routes`]: https://github.com/npm/registry-frontdoor/tree/master/routes
