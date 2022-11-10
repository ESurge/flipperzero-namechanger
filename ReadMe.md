# FlipperZero Name Changer Application

#### Based on FlipperZero Firmware 0.70.x with Flipper App Package Support

## Application

- Name Changer files under `applications/plugins` can be built as a FAP for external loading.
- The app requires a few things:
	- NameChanger Service to be loaded within the firmware
	- `furi_hal_version.c` and `furi_hal_version.h` files need to be modified
	- A function in `api_symbols.csv` needs to be set to `+` after modifying `furi_hal_version`
	
## Service

- Name Changer Service files are under `applications/services`
- `applications/services/application.fam` requires editing
	- `namechangersrv` is the appid
- The service requires a few things:
	- `furi_hal_version.c` and `furi_hal_version.h` files need to be modified
	- A function in `api_symbols.csv` needs to be set to `+` after modifying `furi_hal_version`

## Usage

### Service

- On FlipperZero reboot, `SD Card/dolphin/name.txt` file is read
- If a name exists, the FlipperZero's name gets set to that
- If no name exists or the `Name` field is blank, the current FlipperZero name is saved to the file

### Application

- Launch the Name Changer app
- Select `Change` or `Revert`

#### Change

- On `Change` selection, user is prompted with a text input
- A name of up to 8 characters can be entered
- Upon saving, the FlipperZero will reboot to apply the changed name

#### Revert

- On `Revert` selection, user is prompted with a left/right choice
- Choosing `Cancel` to the left will cancel the revert of the name
- Choosing `Revert` to the right will revert the FlipperZero's name to a blank name
- Upon successful revert, the FlipperZero will reboot, and a blank name results in the default name being used.

## Code Changes

### furi_hal_version.c

- The following code must be added to `furi_hal_version.c`

```
void furi_hal_version_set_custom_name(const char* name) {
    if((name != NULL) && ((strlen(name) >= 1) && (strlen(name) <= 8))) {
        strlcpy(furi_hal_version.name, name, FURI_HAL_VERSION_ARRAY_NAME_LENGTH);
        snprintf(
            furi_hal_version.device_name, FURI_HAL_VERSION_DEVICE_NAME_LENGTH, "xFlipper %s", name);

        furi_hal_version.device_name[0] = AD_TYPE_COMPLETE_LOCAL_NAME;
    }
}
```

### furi_hal_version.h

- The following code must be added to `furi_hal_version.h`

```
void furi_hal_version_set_custom_name(const char* name);
```

### api_symbols.csv

- The following line must be set to `+`

```
Function,+,furi_hal_version_set_custom_name,void,const char*
```
