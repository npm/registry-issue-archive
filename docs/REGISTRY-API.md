# Public Registry API

## Table of Contents

- [Objects](#objects)
  - [Registry](#registry)
  - [Package](#package)
  - [Version](#version)
- [Filtering](#filtering)
- [Errors](#errors)
- [Endpoints](#endpoints)
  - [Meta Endpoints](#meta-endpoints)
    - [`GET·/`](#get)
    - [`GET·/-/all`](#getall)
    - [`GET·/-/all/since`](#getallsince)
  - [User Endpoints](#user-endpoints)
    - [`PUT·/-/user`](#put)
  - [Package Endpoints](#package-endpoints)
    - [`GET·/{package}`](#getpackage)
    - [`GET·/{package}/{version}`](#getpackageversion)
    - [`GET·/{package}/-/{package}-{version}.tgz`](#getpackagepackageversion)

## Objects

### Registry

- `db_name`: "registry"
- `doc_count`: 376841,
- `doc_del_count`: 354,
- `update_seq`: 2889325,
- `purge_seq`: 0,
- `compact_running`: false,
- `disk_size`: 2098360443,
- `data_size`: 1485346312,
- `instance_start_time`: "1471680653634734",
- `disk_format_version`: 6,
- `committed_update_seq`: 2889325

### Package

- `_id`: the package name
- `_rev`: latest revision id
- `name`: the package name
- `description`: description from the `package.json`
- `dist-tags`: an object with at least one key, `latest`, representing dist-tags 
- `versions`: a List of all [Version](#version) objects for the Package
- `time`: an object containing a `created` and `modifited` time stamp
- `author`: object with `name`, `email`, and or `url` of author as listed in `package.json`
- `repository`: object with `type` and `url` of package repository as listed in `package.json`
- `_attachments`: http://docs.couchdb.org/en/2.0.0/intro/api.html#attachments
- `readme`: full text of the `latest` version's `README`

### Version

- `name`: package name,
- `version`: version number
- `homepage`: homepage listed in the `package.json`
- `repository`: object with `type` and `url` of package repository as listed in `package.json`
- `dependencies`: object with dependencies and versions as listed in `package.json`
- `devDependencies`: object with devDependencies and versions as listed in `package.json`
- `scripts`: object with scripts as listed in `package.json`
- `author`: object with `name`, `email`, and or `url` of author as listed in `package.json`
- `license`: as listed in `package.json`
- `readme`: full text of `README` file as pointed to in `package.json`
- `readmeFilename`: name of `README` file
- `_id`: `<name>@<version>`
- `description`: description as listed in `package.json`
- `dist`: and object containing a `shasum` and `tarball` url, usually in the form of `https://registry.npmjs.org/<name>/-/<name>-<version>.tgz` 
- `_npmVersion`: version of npm the package@version was published with
- `_npmUser`: an object containing the `name` and `email` of the npm user who published the package@version
- `maintainers`: and array of objects containing `author` objects as listed in `package.json`
- `directories`:???

## Filtering

## Errors

## Endpoints

### Meta Endpoints

#### `GET·/`

| Name     | Value     | Kind     | Required?     | Notes     |
|------    |-------    |------    |-----------    |-------    |

```
{
  "db_name": "registry",
  "doc_count": 399172,
  "doc_del_count": 354,
  "update_seq": 3351374,
  "purge_seq": 0,
  "compact_running": false,
  "disk_size": 2118398075,
  "data_size": 1600835750,
  "instance_start_time": "1475135224217333",
  "disk_format_version": 6,
  "committed_update_seq": 3351374
}
```

#### `GET·/-/all`

Gets all packages in the registry.

| Name     | Value     | Kind     | Required?     | Notes     |
|------    |-------    |------    |-----------    |-------    |

##### Response

* `200` - [Package](#package) with embedded [Versions](#version)

#### `GET·/-/all/since?startKey={startKey}`

Gets all packages in the registry since `startKey`.

| Name     | Value     | Kind     | Required?     | Notes     |
|------    |-------    |------    |-----------    |-------    |
| startKey | Unix timestamp | **Query** | ❌         | the timestamp from which to start listing packages |

##### Response

* `200` - [Package](#package) with embedded [Versions](#version), added to the registry since the timestamp specified through the `startKey` query string.

### User Endpoints

#### `PUT·/-/user`

Creates a user and validates a login against the registry.

| Name     | Value     | Kind     | Required?     | Notes     |
|------    |-------    |------    |-----------    |-------    |

##### Request payload

```
{
  "name": "user-name",
  "password": "some-password",
  "email": "user@example.org"
}
```

##### Response

* `200` - User exists and can authenticate using the provided credentials
* `201` - User created
```
{
  "ok": "true"
}
```

* `403` - User can not authenticate using the provided credentials (or does not exist)
```
{
  "error": "Invalid login",
  "reason": "The specified login is not valid"
}
```

### Package Endpoints

#### `GET·/{package}`

Gets versions of a specific package.

| Name     | Value     | Kind     | Required?     | Notes     |
|------    |-------    |------    |-----------    |-------    |
| package | String | **Path**  | ✅         | the name of the package (can contain a scope, e.g. `@scope/id`) |
| version | String | **Query** | ❌         | a version number        |
| write   | Boolean | **Query** | ❌         | used when the npm client determines if a package can be updated by the given user (user passed via `Authorize`header) |

##### Response
* `200` - [Package](#package) with embedded [Versions](#version).
* `404` - Package not found.
* `403` - Package can not be updated by the current user (when `write` query string is `true` and `Authorization` header passed)

#### `GET·/{package}/{version}`

Gets a specific package version.

| Name     | Value     | Kind     | Required?     | Notes     |
|------    |-------    |------    |-----------    |-------    |
| package | String | **Path** | ✅         | the name of the package (can contain a scope, e.g. `@scope/id`) |
| version | String | **Path** | ✅         | a version number        |

##### Response
* `200` - [Package](#package) with embedded [Version](#version).
* `404` - Package with specified id or version not found.

#### `GET·/{package}/-/{package}-{version}.tgz`

Gets a specific package version binary.

| Name     | Value     | Kind     | Required?     | Notes     |
|------    |-------    |------    |-----------    |-------    |
| package | String | **Path** | ✅         | the name of the package (can contain a scope, e.g. `@scope/id` - note that scope is not included in the filename part of the URL) |
| version | String | **Path** | ✅         | a version number        |

##### Response
* `200` - Package binary.
* `404` - Package with specified id or version not found.