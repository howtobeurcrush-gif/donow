classDiagram
    direction LR

%% ============================
%%        ACTIVITY LOG
%% ============================
    class ActivityLog {
        +int id
        +int user_id
        +string role
        +string module
        +string action
        +string entity_type
        +int entity_id
        +string description
        +json meta
        +string ip_address
        +string user_agent

        +user()
        +log(module, action, entity_type, entity_id, description, meta)
    }
    ActivityLog --> User : belongsTo


%% ============================
%%        USER
%% ============================
    class User {
        +int id
        +string code
        +string name
        +string email
        +string password
        +string role
        +string status
        +timestamp last_seen_at

        +activityLogs()
        +isOnline()
        +markOnline()
    }
    User --> ActivityLog : hasMany


%% ============================
%%        PRODUCT
%% ============================
    class Product {
        +int id
        +string code
        +string name
        +int volume
        +string unit
        +string category
        +int min_stock
        +int stock_on_hand
        +int max_stock
        +bool is_active
        +string description

        +boms()
        +batches()
        +productHistories()
        +isLowStock()
        +scopeActive()
    }

    Product --> Bom : hasMany
    Product --> Batch : hasMany
    Product --> ProductHistory : hasMany


%% ============================
%%        PRODUCT HISTORY
%% ============================
    class ProductHistory {
        +int id
        +int product_id
        +string action
        +json before
        +json after

        +product()
    }
    ProductHistory --> Product : belongsTo


%% ============================
%%        MATERIAL
%% ============================
    class Material {
        +int id
        +string code
        +string name
        +string unit
        +int stock_on_hand
        +int min_stock
        +int max_stock

        +boms()
        +isLowStock()
        +getStatus()
    }
    Material --> Bom : hasMany


%% ============================
%%        BOM
%% ============================
    class Bom {
        +int id
        +int product_id
        +int material_id
        +float qty

        +product()
        +material()
    }
    Bom --> Product : belongsTo
    Bom --> Material : belongsTo


%% ============================
%%        BATCH
%% ============================
    class Batch {
        +int id
        +int product_id
        +string code
        +date exp_date
        +string status

        +product()
        +ledger()
        +scopeOpen()
        +getLabelAttribute()
    }
    Batch --> Product : belongsTo
    Batch --> Ledger : hasMany


%% ============================
%%        LEDGER
%% ============================
    class Ledger {
        +int id
        +int batch_id
        +string type
        +int qty
        +string reference
        +int user_id
        +json meta

        +batch()
        +user()
        +record(...)
    }

    Ledger --> Batch : belongsTo
    Ledger --> User : belongsTo


%% ============================
%%        ORDER
%% ============================
    class Order {
        +int id
        +string code
        +int user_id
        +string status

        +orderItems()
        +notes()
        +user()
        +addItem()
        +cancel()
    }
    Order --> User : belongsTo
    Order --> OrderItem : hasMany
    Order --> OrderNote : hasMany


%% ============================
%%        ORDER ITEM
%% ============================
    class OrderItem {
        +int id
        +int order_id
        +int product_id
        +int qty
        +int batch_id

        +order()
        +product()
        +batch()
    }

    OrderItem --> Order : belongsTo
    OrderItem --> Product : belongsTo
    OrderItem --> Batch : belongsTo


%% ============================
%%        ORDER NOTE
%% ============================
    class OrderNote {
        +int id
        +int order_id
        +string note
        +string user_name

        +order()
    }
    OrderNote --> Order : belongsTo


%% ============================
%%     PRODUCTION REQUEST
%% ============================
    class ProductionRequest {
        +int id
        +int product_id
        +int qty
        +string status

        +product()
        +approve()
        +reject()
        +schedule()
    }

    ProductionRequest --> Product : belongsTo


%% ============================
%%        PRODUCTION
%% ============================
    class Production {
        +int id
        +int product_id
        +int planned_qty
        +int produced_qty
        +string status
        +timestamp qc_released_at

        +product()
        +releaseQc()
        +isCompleted()
        +fail()
    }

    Production --> Product : belongsTo
