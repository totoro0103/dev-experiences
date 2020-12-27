## Helper
### Localstorage

```js
import { log } from 'helpers/debug';

/**
 * Safely remove a value from localStorage
 *
 * @param {string} key the key of the localStorage item to remove
 */
export const clearLocalStorage = key => {
	try {
		return window.localStorage.removeItem( key );
	} catch ( err ) {
		log( 'localStorage write error', err );
	}
};

/**
 * Safely read a value from localStorage
 *
 * @param {string} key the key of the localStorage item to read
 */
export const readLocalStorage = key => {
	try {
		const storedData = window.localStorage.getItem( key );
		if ( null === storedData ) {
			return null;
		}

		const { expires, value } = JSON.parse( storedData ) || {};

		// If the value has expired, tidy up the item and return null
		if ( expires && expires < Date.now() ) {
			clearLocalStorage( key );
			return null;
		}

		return value;
	} catch ( err ) {
		log( 'localStorage read error', err );
		return null;
	}
};

/**
 * Safely write a value to localStorage with an optional expiry time
 *
 * @param {string} key the localStorage key
 * @param {*} value the localStorage value to save
 * @param {number} expires time in ms before the saved key/value expires. 0 = no expiry
 */
export const writeLocalStorage = ( key, value, expires = 0 ) => {
	try {
		const data = { value };

		if ( expires ) {
			data.expires = Date.now() + expires;
		}

		return window.localStorage.setItem( key, JSON.stringify( data ) );
	} catch ( err ) {
		log( 'localStorage write error', err );
	}
};

```

### Generate id

```js
const rc = () => Math.floor( ( 1 + Math.random() ) * 0x10000 ).toString( 16 ).substring( 1 );

export const uniqid = ( str, length ) => {
	if ( length <= 0 ) {
		return str + rc();
	}

	return uniqid( str + rc(), length - 1 );
};

export const generateId = () => uniqid( '', 8 );

```

###  Email regex
```js
// Export for reuse in other contexts.
export const emailRegexString = '(?:[a-z0-9!#$%&\'*+/=?^_`{|}~-]+(?:\\.[a-z0-9!#$%&\'*+/=?^_`{|}~-]+)*|"(?:[\\x01-\\x08\\x0b\\x0c\\x0e-\\x1f\\x21\\x23-\\x5b\\x5d-\\x7f]|\\\\[\\x01-\\x09\\x0b\\x0c\\x0e-\\x7f])*")@(?:(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?|\\[(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?|[a-z0-9-]*[a-z0-9]:(?:[\\x01-\\x08\\x0b\\x0c\\x0e-\\x1f\\x21-\\x5a\\x53-\\x7f]|\\\\[\\x01-\\x09\\x0b\\x0c\\x0e-\\x7f])+)\\])';

// Not exporting; prefer use of helper function below.
const emailValidationRegex = new RegExp( `^${emailRegexString}$`, 'i' );

/**
 * Check to make sure the email is valid
 *
 * @param  {string}  email
 * @return {bool}
 */
export const isValidEmail = email => emailValidationRegex.test( email );
```

