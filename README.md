# API Documentation Repository

Welcome to the API Documentation repository. This project hosts comprehensive API documentation using GitHub Pages and OpenAPI specifications.

## 📚 Overview

This repository contains:
- **OpenAPI Specifications**: Machine-readable API definitions in YAML format
- **API Documentation**: Human-readable guides and references
- **Examples**: Sample requests and responses
- **Hosted Documentation**: Live documentation site via GitHub Pages

## 🗂️ Repository Structure

```
API-Documentation/
├── api-specs/          # OpenAPI specification files
│   └── osm-api.yaml    # Online Scout Manager API spec
├── docs/               # Documentation guides and tutorials
├── examples/           # Sample requests and responses
├── .github/            # GitHub configuration
│   └── workflows/      # CI/CD workflows
├── _config.yml         # Jekyll configuration
└── index.md            # GitHub Pages landing page
```

## 🔗 Available APIs

### Online Scout Manager (OSM) API

Complete API documentation for managing scout sections, members, events, badges, invoices, and patrols.

- **Specification**: [`api-specs/osm-api.yaml`](api-specs/osm-api.yaml)
- **Base URL**: `https://www.onlinescoutmanager.co.uk`
- **Authentication**: OAuth 2.0 Bearer Token
- **Version**: 1.0.0

**Key Features**:
- Member management and custom data operations
- Invoice and payment tracking
- Event management and attendance
- Badge progress and records
- Patrol creation and assignments

## 🚀 Using This Documentation

### Viewing the Hosted Documentation

Visit the live documentation site at: [https://mmollart.github.io/API-Documentation/](https://mmollart.github.io/API-Documentation/)

### Using the OpenAPI Specifications

#### With Swagger UI

```bash
# Clone the repository
git clone https://github.com/MMollart/API-Documentation.git

# Open the specification in Swagger Editor
# Visit: https://editor.swagger.io/
# File → Import File → Select api-specs/osm-api.yaml
```

#### With Postman

1. Open Postman
2. Click **Import**
3. Select **File** → Choose `api-specs/osm-api.yaml`
4. The API collection will be created automatically

#### With Code Generation

Generate client SDKs in multiple languages:

```bash
# Install OpenAPI Generator
npm install @openapitools/openapi-generator-cli -g

# Generate Python client
openapi-generator-cli generate -i api-specs/osm-api.yaml -g python -o ./clients/python

# Generate JavaScript client
openapi-generator-cli generate -i api-specs/osm-api.yaml -g javascript -o ./clients/javascript
```

## 📖 Documentation Guidelines

### Adding New API Specifications

1. Create a new YAML file in `api-specs/` directory
2. Follow OpenAPI 3.0.3 specification format
3. Include comprehensive descriptions and examples
4. Update this README with links to the new spec

### Contributing to Documentation

1. Fork the repository
2. Create a feature branch (`git checkout -b docs/new-api`)
3. Make your changes
4. Commit with descriptive messages
5. Push to your fork
6. Open a pull request

### Documentation Standards

- Use OpenAPI 3.0.3 format for API specifications
- Include realistic examples in all schemas
- Document all OAuth scopes and security requirements
- Provide clear descriptions for all parameters
- Include error responses (401, 403, 404, 429, 500)

## 🔐 Authentication

Most APIs in this repository require OAuth 2.0 authentication. Refer to individual API specifications for specific authentication requirements.

### OAuth Scopes (OSM API)

- `section:member:read` - Read member information
- `section:member:write` - Update member data
- `section:finance:read` - View financial data
- `section:finance:write` - Manage invoices and payments
- `section:event:read` - Access event information
- `section:badge:read` - View badge records
- `section:admin:write` - Administrative operations
- `section:attendance:write` - Manage attendance and patrols

## 🛠️ Development Tools

### Validating OpenAPI Specifications

```bash
# Using Swagger CLI
npm install -g @apidevtools/swagger-cli
swagger-cli validate api-specs/osm-api.yaml

# Using OpenAPI Generator
openapi-generator-cli validate -i api-specs/osm-api.yaml
```

### Local Development

To run the documentation site locally:

```bash
# Install Jekyll
gem install bundler jekyll

# Clone the repository
git clone https://github.com/MMollart/API-Documentation.git
cd API-Documentation

# Install dependencies
bundle install

# Run local server
bundle exec jekyll serve

# Visit http://localhost:4000
```

## 📝 Version Control

This repository follows semantic versioning for API specifications:

- **Major version** (`v2.0.0`): Breaking changes to API endpoints or schemas
- **Minor version** (`v1.1.0`): New features or endpoints (backward compatible)
- **Patch version** (`v1.0.1`): Bug fixes, documentation updates, clarifications

## 🤝 Contributing

Contributions are welcome! Please see our contributing guidelines:

1. Check existing issues or create a new one
2. Fork the repository
3. Create a feature branch
4. Make your changes with clear commit messages
5. Validate OpenAPI specs before committing
6. Submit a pull request

## 📄 License

This documentation repository is maintained by Matt Mollart. Individual API specifications may have their own licensing terms.

## 🔗 Related Projects

- [OSM API Client](https://github.com/MMollart/osm) - Python client for Online Scout Manager API
- [Fireworks Analysis](https://github.com/MMollart/fireworks) - Event booking analysis CLI

## 📬 Contact

For questions or suggestions about this documentation:

- **GitHub Issues**: [Create an issue](https://github.com/MMollart/API-Documentation/issues)
- **Repository Owner**: [@MMollart](https://github.com/MMollart)

---

**Last Updated**: October 28, 2025
