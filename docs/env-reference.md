---
prev: ./guide.html
next: ./plugins.html
---

# Environment Reference

When creating a new project, the main controller of your configuration is the .env file located at the root of your project.
The following reference page is all the available options for your CirculateJS application.

## ENV Values
The following are the values that can be set:

### **ENV**
* Type: String
* Default Value: "production"

This is used to set your environment to either Production or Development. 
The value for development just needs to be set to "development".

---

### **APP_NAME**
* Type: String
* Default Value: "CirculateJS"

This sets the name of the application.

---

### **HOST**
* Type: String
* Default Value: "localhost"

This sets the host for the application.

---

### **PORT**
* Type: Number
* Default Value: 3000

This sets the port for the application.

---

### **DB**
* Type: String
* Default Value: "sqlite"

This sets your database type. This value defaults to SQLite for development. 
This value can also be set to "mysql".

> Currently, we only support SQLite and MySQL, but we plan on adding more options in the future.

---

### **DB_NAME**
* Type: String
* Default Value: "circulatejs"

Sets the name your your database.

---

### **DB_HOST**
* Type: String
* Only required if **DB** set to "mysql"
* Default Value: "127.0.0.1"

This sets the host of your database server. This isn't needed if your running SQLite.

---

### **DB_PORT**
* Type: Number
* Only required if **DB** set to "mysql"
* Default Value: 3306

This sets the post of your database server. This isn't needed if your running SQLite.

---

### **DB_USER**
* Type: String
* Only required if **DB** set to "mysql"
* No Default Value

This sets the user of your database server. This isn't needed if your running SQLite.

---

### **DB_PASSWORD**
* Type: String
* Only required if **DB** set to "mysql"
* No Default Value

This sets the password of your database server. This isn't needed if your running SQLite.

---

### **AUTH_KEY**
* Type: String
* No Default Value

This is the authentication key for your JWT authentication. This will be auth generated when you create a new application, but can be changed to whatever you see fit.

---

### **ADMIN_LOCATION**
* Type: String
* Default Value: "/admin"

This sets the path for the admin interface url. This can be changed to whatever you want, just make sure you don't have a conflicting path.

---

### **API_PATH**
* Type: String
* Default Value: "/api"

This sets the api path for your application. This can be changed to whatever you want, just make sure you don't have a conflicting path.just make sure you 

---

### **PLUGINS_PATH**
* Type: String
* Default Value: "/plugins"

This sets the plugin path. If this is changed, make sure the plugin directory is set to the same path. This path is set by default when creating a new application.  If you don't plan on changing the plugin path, you can omit it from the env file entirely.
