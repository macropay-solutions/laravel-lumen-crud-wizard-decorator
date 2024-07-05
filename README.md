# laravel-lumen-crud-wizard-decorator

This is a decorator lib that can be used with [Laravel crud wizard](https://github.com/macropay-solutions/laravel-lumen-crud-wizard).

It renames/maps the column names for the resource and its relations, and it can also compose additional columns from the resource columns and from its relations' columns.
Also, it can restrict the number of columns returned to the requested ones.

Code exmaple:

    namespace MacropaySolutions\LaravelCrudWizardDecorator\Decorators;
    
    class ExampleDecorator extends AbstractResourceDecorator
    {
        public function getResourceMappings(): array
        {
            return [
                'id' => 'ID',
                'updated_at' => 'updatedAt',
                'created_at' => 'createdAt',
            ];
        }
    
        /**
         * @inheritDoc
         */
        public function getRelationMappings(): array
        {
            return [
                'roleRelation' => [
                    'name' => 'roleRelationName',
                    'color' => 'roleRelationColor',
                ],
            ];
        }
    
        /**
         * @inheritDoc
         */
        public function getComposedColumns(): array
        {
            return [
                'hasRelations' => fn(array $row): bool => $this->getHasRoleRelations($row),
                'roleNameColor' => fn(array $row): ?string => $this->getNameColorRoleRelation($row),
            ];
        }
    
        private function getHasRoleRelations(array $row): bool
        {
            return isset($row['role_relation']);
        }
    
        private function getNameColorRoleRelation(array $row): ?string
        {
            return $this->getHasRoleRelations($row) ?
                ($row['role_relation']['name'] ?? '') . ' ' . ($row['role_relation']['name'] ?? '') :
                null;
        }
    }



 [Crud routes](#-crud-routes)

1. [Create resource](#1-create-resource)

2. [Get resource](#2-get-resource)

3. [List filtered resource](#3-list-filtered-resource)

4. [Update resource (or create)](#4-update-resource-or-create)

5. [Delete resource](#5-delete-resource)





###  Crud routes


#### 1 Create resource
**POST** /{resource}

headers:

      
      
      Accept: application/json
      
      ContentType: application/json

body:

      {
         "roleID": "1",
      }

Json Response:

200:

    {
        "success": true,
        "code": 201,
        "locale": "en",
        "message": "success",
        "data": {
            "ID": 3,
            "roleID": "1",
            "updatedAt": "2022-10-27 09:05:49",
            "createdAt": "2022-10-27 09:04:46",
            "roleRelationName": "name",
            "roleRelationColor": "blue",
            "hasRelations": true,
            "roleNameColor": "name blue",
            "pki": "3"
        }
    }

    {
        "success": false,
        "code": 400,
        "locale": "en",
        "message": "The given data was invalid: The role id field is required.",
        "data": {
            "roleID": [
                "The role id field is required."
            ]
        }
    }



#### 2 Get resource
**GET** /info/{resource}/{identifier}

headers:

      
      
      Accept: application/json

Json Response:

200:

    {
        "success": true,
        "code": 200,
        "locale": "en",
        "message": "success",
        "data": {
            "ID": 3,
            "roleID": "1",
            "updatedAt": "2022-10-27 09:05:49",
            "createdAt": "2022-10-27 09:04:46",
            "roleRelationName": "name",
            "roleRelationColor": "blue",
            "hasRelations": true,
            "roleNameColor": "name blue",
            "pki": "3"
        }
    }

    {
        "success": false,
        "code": 400,
        "locale": "en",
        "message": "Not found",
        "data": null
    }


#### 3 List filtered resource
**GET** /{resource}?perPage=10&page=2

**GET** /{resource}/{identifier}/{relation}?...

headers:

      
      
      Accept: application/json or text/csv or application/xls


Json Response:

200:

    {
        "success": true,
        "code": 200,
        "locale": "en",
        "message": "success",
        "data": {
            "sums": null,
            "avgs": null,
            "mins": null,
            "maxs": null,
            "current_page": 1,
            "data": [
                {
                    "ID": 3,
                    "roleID": "1",
                    "updatedAt": "2022-10-27 09:05:49",
                    "createdAt": "2022-10-27 09:04:46",
                    "roleRelationName": "name",
                    "roleRelationColor": "blue",
                    "hasRelations": true,
                    "roleNameColor": "name blue",
                    "pki": "3"
                }
            ],
            "from": 1,
            "last_page": 1,
            "per_page": 10,
            "to": 1,
            "total": 1,
            "filterable": [
                "ID",
                "roleID",
                "updatedAt",
                "createdAt",
                "roleRelationName",
                "roleRelationColor"
            ],
            "sortable": [
                "ID",
                "roleID"
            ],
            "relations": [
                "roleRelation"
            ]
        }
    }


#### 4 Update resource (or create)
**PUT** /{resource}/{identifier}

headers:

      
      
      Accept: application/json
      
      ContentType: application/json

body:

      {
        "roleID": "2"
      }

Json Response:

200:

    {
        "success": true,
        "code": 200, // or 201 for upsert
        "locale": "en",
        "message": "success",
        "data": {
            "ID": 3,
            "roleID": "2",
            "updatedAt": "2022-10-27 09:05:49",
            "createdAt": "2022-10-27 09:04:46",
            "roleRelationName": "name2",
            "roleRelationColor": "green",
            "hasRelations": true,
            "roleNameColor": "name2 green",
            "pki": "3"
        }
    }

    {
        "success": false,
        "code": 400,
        "locale": "en",
        "message": "The given data was invalid: The role id field is required.",
        "data": {
            "roleID": [
                "The role id field is required."
            ]
        }
    }


#### 5 Delete resource
**DELETE** /{resource}/{identifier}

headers:

      

Json Response:

200:

    {
        "success": true,
        "code": 204,
        "locale": "en",
        "message": "success",
        "data": null
    }

    {
        "success": false,
        "code": 400,
        "locale": "en",
        "message": "Not found",
        "data": null
    }
