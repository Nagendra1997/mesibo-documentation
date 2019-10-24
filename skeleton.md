This repository contains a Bare bones version of a Mesibo Module that explains the usage of different aspects of the module, various callback functions, callable functions and utilities.

The Skeleton Module serves as a reference for all other Sample Modules. For complete documentation refer [Loadable Modules - add your own custom features to Mesibo](Loadable Modules - add your own custom features to Mesibo)

### 1. The C/C++ Source file
The module name is `skeleton`. The C/C++ Source file ie; `skeleton.cpp`. The header file `module.h` containing the definitions of all module related components is included in the C/C++ source as follows:
```cpp
#include "module.h"
```
### 2. Configuring the skeleton module
While loading your module you need to provide your configuration details in the file `/etc/mesibo/mesibo.conf`. The example configuration is provided in the file `skeleton.conf`. Copy the configuration from `skeleton.conf`into `mesibo.conf` located at `/etc/mesibo`.

The skeleton module is configured as follows:
```

module=skeleton {
source=skeleton.cpp
name=skeleton
description=Bare bones version of a Mesibo Module
}

```
Please note that the above is just an example configuration. You can pass name-value pairs as per the configuration needs of your module. Any configuration paramters that you pass will be available through the initialisation function.

### 3. Initializing the skeleton module
The skeleton module is initialized with the Mesibo Module Configuration details - module version, the name of the module and  references to the module callback functions.
```cpp

int mesibo_module_skeleton_init(mesibo_int_t version, mesibo_module_t *m,
				mesibo_uint_t len, module_configs_t *config) {
	if(MESIBO_MODULE_VERSION != version) {
		m->log(m, 0, " module version mismatch\n");
		return -1;
	}

	if(sizeof(mesibo_module_t) != len) {
		m->log(m, 0, " module size mismatch\n");
		return -1;
	}

	if(MESIBO_MODULE_SIGNATURE != m->signature) {
		m->log(m, 0, " module signature mismatch\n");
		return -1;
	}

	if(config) {
		m->log(m, 0, " Following configuration item(s) were passed to the module\n");
		for(int i=0; i < config->count; i++) {
			m->log(m, 0, " module config item: name %s value %s\n", 
					config->items[i].name, config->items[i].value);
		}
	}

	m->version = 1;
	m->flags = 0;
	m->name = strdup("Sample Skeleton Module");
	m->cleanup = skeleton_cleanup;
	m->on_message = skeleton_on_message;
	m->on_message_status = skeleton_on_message_status;
	m->on_login = skeleton_on_login;
	m->log(m, 0, "================> %s init called\n", m->name);
	return 0;

}

```



