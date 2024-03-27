# Veeva Vault and AEM Integration

This README provides information on integrating Veeva Vault with Adobe Experience Manager (AEM) using three APIs. These APIs facilitate authentication, querying documents, and retrieving thumbnail images from Veeva Vault.

## Authentication

- **Endpoint**: `/api/v23.3/auth`
- **Request Body**:
    - `username`: Your Veeva Vault username
    - `password`: Your Veeva Vault password
- **Request Type**: POST
- **Response**: Contains a sessionId for accessing other APIs.

## Submitting a Query

- **Endpoint**: `/api/v23.3/query`
- **Request Header**:
    - `Authorization`: sessionId
    - `Accept`: application/json
    - `X-VaultAPI-DescribeQuery`: true
    - `Content-Type`: application/x-www-form-urlencoded
- **Request Body**:
    - `q`: Query string
- **Request Type**: POST
- **Response**: Contains a document list based on the query parameters.

### Example Query

```sql
SELECT id, version_id, name__v, filename__v, production_cdn_url__v, version_modified_date__v, size__v, format__v, status__v, document_number__v, major_version_number__v, minor_version_number__v
FROM documents
WHERE type__v = 'Material' AND (status__v = 'Approved for Distribution' OR status__v = 'Obsolete') AND version_modified_date__v BETWEEN '2024-01-01T00:00:00.000Z' AND '2024-03-20T18:35:00.124Z'
  PAGESIZE 2
```

- `type__v`: Document type
- `status__v`: Document status
- `version_modified_data__v`: Last modified date

## Thumbnail Image

- **Endpoint**: `/api/v23.3/objects/${document-id}/${major-version}/${minor-version}/thumbnail`
- **Request Header**:
  - `Authorization`: sessionId
  - `Accept`: application/json
  - `Content-Type`: application/x-www-form-urlencoded
- **Request Params**:
  - `Document ID`
  - `Latest major version`
  - `Minor version`
- **Request Type**: GET
- **Example Call**: `/api/v23.3/objects/documents/1302/versions/3/0/thumbnail`

The thumbnail API call depends on the document id, so it needs to be called for each document available in the second API response.

## Assumptions

- The expected PDF documents will be created against the fixed document type which is "Material".
- The documents will be picked up with the defined states "Approved for Distribution" or "Expired".
- The timing (sync time and current time) will be managed by the integration.
- The thumbnail call is dependent on the document_id; this aggregation needs to be managed by the integration.


## Aggregated Response Body

After integrating Veeva Vault with AEM using the provided APIs, the final aggregated response body would resemble the following JSON format:

```json
[
  {
    "id": 123456789,
    "version_id": "abc123",
    "document_number__v": "DOC001",
    "filename__v": "example_document1.pdf",
    "name__v": "Example Document 1",
    "format__v": "pdf",
    "production_cdn_url__v": "https://example.cloudfront.net/documents/example_document1.pdf",
    "size__v": 2048,
    "major_version_number__v": 1,
    "minor_version_number__v": 0,
    "status__v": "active",
    "thumbnail": "base64_encoded_thumbnail_data1"
  },
  {
    "id": 987654321,
    "version_id": "xyz789",
    "document_number__v": "DOC002",
    "filename__v": "example_document2.pdf",
    "name__v": "Example Document 2",
    "format__v": "pdf",
    "production_cdn_url__v": "https://example.cloudfront.net/documents/example_document2.pdf",
    "size__v": 4096,
    "major_version_number__v": 2,
    "minor_version_number__v": 1,
    "status__v": "inactive",
    "thumbnail": "base64_encoded_thumbnail_data2"
  },
  {
    "id": 246813579,
    "version_id": "def456",
    "document_number__v": "DOC003",
    "filename__v": "example_document3.pdf",
    "name__v": "Example Document 3",
    "format__v": "pdf",
    "production_cdn_url__v": "https://example.cloudfront.net/documents/example_document3.pdf",
    "size__v": 8192,
    "major_version_number__v": 3,
    "minor_version_number__v": 2,
    "status__v": "active",
    "thumbnail": "base64_encoded_thumbnail_data3"
  }
]

```
This should provide a comprehensive guide on integrating Veeva Vault with AEM and understanding the provided APIs.