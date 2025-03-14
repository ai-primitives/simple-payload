# simple-payload

Simplified Data Schema Definition for [Payload CMS](https://payloadcms.com) applications using concise syntax.

## Overview

`simple-payload` is a lightweight schema generator for Payload CMS that enables extremely concise data model definitions. This package focuses purely on transforming a simplified schema format into Payload CMS compatible configuration, making it easier to define your data models with minimal code.

## Features

- **Simplified Schema Definition** - Define your data models with minimal code
- **Relationship Management** - Easily define relationships between collections
- **Select Field Shorthand** - Define select fields with predefined options using pipe separators
- **Join Fields** - Simplified syntax for defining reverse relationships (join fields)
- **Full Payload CMS Compatibility** - Generates standard Payload CMS configuration
- **Type Safety** - Full TypeScript support for your data models
- **Zero Dependencies** - No runtime dependencies, only uses Payload types for development

## Installation

```bash
npm install simple-payload
# or
yarn add simple-payload
# or
pnpm add simple-payload
```

## Quick Start

```javascript
// Import necessary packages
import { DB, configureDatabase } from 'simple-payload';
import { buildConfig } from 'payload/config';

// Define your schema with simplified syntax
const { collections, collectionRefs } = DB({
  posts: {
    title: 'text',
    content: 'richtext',
    status: 'Draft | Published | Archived', // Select field with predefined options
    contentType: 'Text | Markdown | Code | Object | Schema', // Another select field example
    tags: 'tags[]',
    author: 'authors'
  },
  tags: {
    name: 'text',
    posts: '<-posts.tags'  // Join field to posts (reverse relation)
  },
  authors: {
    name: 'text',
    email: 'email',
    role: 'Admin | Editor | Writer', // Select field with predefined options
    posts: '<-posts.author'  // Join field to posts (reverse relation)
  }
});

// Configure your database
dbConfig.config.db = configureDatabase({
  type: 'mongodb',
  uri: process.env.MONGODB_URI || 'mongodb://localhost:27017/my-database'
});

// Initialize Payload with the generated configuration
// For Payload 3.0 with NextJS, you would typically use this in your payload.config.ts file
export default buildConfig({
  // Your other Payload config options
  collections: dbConfig.config.collections,
  db: dbConfig.config.db,
});
```

## Field Types

Here are the supported field types and their mappings to Payload CMS field types:

| payload-db syntax | Payload CMS field type |
|-------------------|-------------------------|
| `'text'`          | Text field              |
| `'richtext'`      | Rich Text field         |
| `'number'`        | Number field            |
| `'date'`          | Date field              |
| `'email'`         | Email field             |
| `'checkbox'`      | Checkbox field          |
| `'json'`          | JSON field              |
| `'Option1 \| Option2 \| Option3'` | Select field with options |
| `'collection'`    | Relationship field      |
| `'collection[]'`  | Relationship (hasMany)  |
| `'<-collection.field'`  | Join field (reverse relation) |
| `'tags'`          | Select field (hasMany)  |
| `'tags[]'`        | Array of Select fields  |

## Relationship Syntax

payload-db simplifies relationship definitions:

```javascript
const db = DB({
  posts: {
    // ... other fields
    author: 'authors', // Relationship to a single author
    relatedPosts: 'posts[]', // Relationship to multiple posts
  },
  authors: {
    // ... other fields
    posts: '<-posts.author' // Join field to posts (reverse relation)
  }
});
```

## Database Configuration

Configure your database adapter directly in your Payload configuration:

```javascript
// Get collections from payload-db
const { collections } = DB({
  // Your schema definition
});

// Initialize Payload with the collections and your database adapter
payload.init({
  // ...other config
  collections,
  db: {
    // MongoDB
    adapter: 'mongoose',
    url: 'mongodb://localhost:27017/my-db'
    
    // Or PostgreSQL
    // adapter: 'postgres',
    // url: 'postgres://user:pass@localhost:5432/my-db'
    
    // Or SQLite
    // adapter: 'sqlite',
    // url: 'sqlite://path/to/database.sqlite'
  }
});
```

To use MongoDB or PostgreSQL, ensure you have installed the appropriate adapter package:

```bash
npm install @payloadcms/db-mongodb
# or
npm install @payloadcms/db-postgres
```

## Advanced Usage

You can pass additional configuration options to the DB function:

```javascript
const { collections, collectionRefs } = DB({
  // Collections definition
}, {
  // Additional configuration options
  customOption: 'value',
});

// You can further customize the collections before passing to Payload
const customizedCollections = collections.map(collection => {
  // Add custom hooks, access control, etc.
  return {
    ...collection,
    hooks: {
      beforeChange: [
        // Your custom hooks
      ]
    }
  };
});

// Then use the customized collections with Payload
payload.init({
  // ...other config
  collections: customizedCollections,
});
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT
