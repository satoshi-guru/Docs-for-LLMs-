---
title: "ExpoContacts"
url: https://docs.expo.dev/versions/latest/sdk/contacts
---

# ExpoContacts

# Expo Contacts

A library that provides access to the phone's system contacts.

Android

iOS

Included in Expo Go

Ask AI

[GitHub](https://github.com/expo/expo/tree/sdk-56/packages/expo-contacts)[npm](https://www.npmjs.com/package/expo-contacts)[Changelog](https://github.com/expo/expo/tree/sdk-56/packages/expo-contacts/CHANGELOG.md)

Bundled version:

~56.0.7

Copy page

* * *

> For the complete documentation index, see [llms.txt](/llms.txt). Use this file to discover all available pages.

`expo-contacts` provides access to the device's system contacts, allowing you to get contact information as well as add, edit, or remove contacts. On iOS, contacts have a multi-layered grouping system that you can also access through this API.

## Installation

Terminal

Copy

`- ``npx expo install expo-contacts`

If you are installing this in an [existing React Native app](/bare/overview), make sure to [install `expo`](/bare/installing-expo-modules) in your project.

## Configuration in app config

You can configure `expo-contacts` using its built-in [config plugin](/config-plugins/introduction) if you use config plugins in your project ([Continuous Native Generation (CNG)](/workflow/continuous-native-generation)). The plugin allows you to configure various properties that cannot be set at runtime and require building a new app binary to take effect. If your app does not use CNG, then you'll need to manually configure the library.

### Example app.json with config plugin

app.json

Copy


    {
      "expo": {
        "plugins": [
          [
            "expo-contacts",
            {
              "contactsPermission": "Allow $(PRODUCT_NAME) to access your contacts."
            }
          ]
        ]
      }
    }


### Configurable properties

Name| Default| Description
---|---|---
`contactsPermission`| `"Allow $(PRODUCT_NAME) to access your contacts"`| Only for: iOS
A string to set the `NSContactsUsageDescription` permission message.

Are you using this library in an existing React Native app?

If you're not using Continuous Native Generation ([CNG](/workflow/continuous-native-generation)) (you're using native android and ios projects manually), then you need to configure following permissions in your native projects:

  * For Android, add `android.permission.READ_CONTACTS` and `android.permission.WRITE_CONTACTS` permissions to your project's android/app/src/main/AndroidManifest.xml:

        <uses-permission android:name="android.permission.READ_CONTACTS" />
        <uses-permission android:name="android.permission.WRITE_CONTACTS" />


  * For iOS, add the `NSContactsUsageDescription` key to your project's ios/[app]/Info.plist:

        <key>NSContactsUsageDescription</key>
        <string>Allow $(PRODUCT_NAME) to access your contacts</string>



## Usage

Contacts Manipulations

Contacts Manipulations

Copy


    const contact = await Contact.create({ givenName: 'John', familyName: 'Doe' });
    // { givenName: "John", familyName: "Doe"}

    await contact.setGivenName('Andrew');
    // { givenName: "Andrew", familyName: "Doe"}

    await contact.addPhone({ label: 'work', number: '+12345678912' });
    // { givenName: "Andrew", familyName: "Doe", phones: [{label: "work", number: "+12345678912"}]}

    const phones = await contact.getPhones();
    // Changes only the defined fields, leaves the rest unchanged
    await contact.patch({ phones: [...phones, { label: 'home', number: '+98765432198' }] });
    /*
    {
      givenName: "Andrew",
      familyName: "Doe",
      phones: [
        {label: "work", number: "+12345678912"},
        {label: "home", number: "+98765432198"}
      ]
    }
    */

    // Replaces all fields with the ones defined in the object
    await contact.update({ givenName: 'John', familyName: 'Doe' });
    // { givenName: "John", familyName: "Doe"}


Retrieving contacts

Retrieving contacts

Copy


    const contactDetails = await Contact.getAllDetails([ContactField.FULL_NAME, ContactField.PHONES], {
      limit: 20,
      offset: 10,
      sortOrder: ContactsSortOrder.GivenName,
    });

    // Contact instance can be created from fetched details
    const contacts = contactDetails.map(item => new Contact(item.id));

    const contactsFromGetAll = await Contact.getAll({
      limit: 20,
      offset: 10,
      sortOrder: ContactsSortOrder.GivenName,
    });


Contacts infinite scroll example

Contacts infinite scroll example

Copy


    import { Contact, ContactField, PartialContactDetails } from 'expo-contacts';
    import { useEffect, useState } from 'react';
    import { FlatList, Text, View } from 'react-native';

    const FIELDS = [ContactField.FULL_NAME, ContactField.PHONES] as const;

    export default function InfiniteContacts() {
      const [contactDetails, setContactDetails] = useState<PartialContactDetails<typeof FIELDS>[]>([]);

      useEffect(() => {
        loadMore();
      }, []);

      const loadMore = async () => {
        const newBatch = await Contact.getAllDetails(FIELDS, {
          limit: 20,
          offset: contactDetails.length,
        });
        setContactDetails(prev => [...prev, ...newBatch]);
      };

      return (
        <View style={{ flex: 1 }}>
          <FlatList
            data={contactDetails}
            keyExtractor={item => item.id}
            onEndReached={loadMore}
            onEndReachedThreshold={0.5}
            renderItem={({ item }) => (
              <View style={{ padding: 10, borderBottomWidth: 1, borderColor: '#ccc' }}>
                <Text>{item.fullName}</Text>
                <Text>{item.phones[0]?.number ?? 'No phone number'}</Text>
              </View>
            )}
          />
        </View>
      );
    }


Edit contact form example

Edit contact form example

Copy


    import { Contact, ContactField, ContactPatch } from 'expo-contacts';
    import { useEffect, useState } from 'react';
    import { Alert, Button, ScrollView, Text, TextInput, View } from 'react-native';

    export default function ContactForm() {
      const [contact, setContact] = useState<Contact | null>(null);
      const [contactPatch, setContactPatch] = useState<ContactPatch>({});
      const [newPhoneInput, setNewPhoneInput] = useState('');

      useEffect(() => {
        Contact.getAll({ limit: 1 }).then(async ([first]) => {
          if (first) {
            setContact(first);
            setContactPatch(await first.getDetails([ContactField.GIVEN_NAME, ContactField.PHONES]));
          }
        });
      }, []);

      if (!contactPatch && contact) {
        return <Text style={{ marginTop: 50 }}>Loading details...</Text>;
      }

      const handleChangeName = (text: string) =>
        setContactPatch(prev => ({ ...prev, givenName: text }));

      const handleAddPhone = () => {
        if (!newPhoneInput) {
          return;
        }
        setContactPatch(prev => ({
          ...prev,
          phones: [...(prev.phones || []), { label: 'mobile', number: newPhoneInput }],
        }));
        setNewPhoneInput('');
      };

      const handleRemovePhone = (idx: number) =>
        setContactPatch(prev => ({
          ...prev,
          phones: prev.phones?.filter((_, i) => i !== idx),
        }));

      const handlePatch = async () => {
        if (contact) {
          await contact.patch(contactPatch);
        }
        Alert.alert('Contact patched successfully');
      };

      if (!contact) {
        return <Text style={{ marginTop: 50 }}>Loading...</Text>;
      }

      return (
        <ScrollView style={{ padding: 20, paddingTop: 60 }}>
          <Text style={{ fontWeight: 'bold', marginBottom: 10 }}>Edit ID: {contact.id}</Text>

          <Text>Name:</Text>
          <TextInput
            style={{ borderWidth: 1, padding: 5, marginBottom: 10 }}
            value={contactPatch.givenName || ''}
            onChangeText={handleChangeName}
          />

          <Text>Phones:</Text>
          {contactPatch.phones?.map((phone, index) => (
            <View key={index} style={{ flexDirection: 'row', alignItems: 'center', marginBottom: 5 }}>
              <Text style={{ flex: 1 }}>{phone.number}</Text>
              <Button title="Remove" onPress={() => handleRemovePhone(index)} />
            </View>
          ))}

          <View style={{ flexDirection: 'row', marginBottom: 20, marginTop: 10 }}>
            <TextInput
              style={{ borderWidth: 1, flex: 1, padding: 5, marginRight: 5 }}
              value={newPhoneInput}
              onChangeText={setNewPhoneInput}
              placeholder="New phone..."
            />
            <Button title="Add" onPress={handleAddPhone} />
          </View>

          <Button title="PATCH CONTACT" onPress={handlePatch} />
        </ScrollView>
      );
    }


## API


    import { Contact } from 'expo-contacts';


## Component

### `ContactAccessButton`

iOS 18.0+

Type: `React.[PureComponent](https://react.dev/reference/react/PureComponent)<ContactAccessButtonProps>`

Creates a contact access button to quickly add contacts under limited-access authorization.

For more details, you can read the Apple docs about the underlying [`ContactAccessButton`](https://developer.apple.com/documentation/contactsui/contactaccessbutton) SwiftUI view.

ContactAccessButtonProps

### `backgroundColor`

iOS 18.0+

Optional • Type: `[ColorValue](https://reactnative.dev/docs/colors)`

A color of the button's background. Provided color should not be transparent, otherwise it may not satisfy platform requirements for button legibility.

### `caption`

iOS 18.0+

Optional • Literal type: `string`

When the query produces a single result, the contact access button shows the caption under the matching contact name. It can be nothing (default), email address or phone number.

Acceptable values are: `'default'` | `'email'` | `'phone'`

### `ignoredEmails`

iOS 18.0+

Optional • Type: `string[]`

An array of email addresses. The search omits contacts matching query that also match any email address in this array.

### `ignoredPhoneNumbers`

iOS 18.0+

Optional • Type: `string[]`

An array of phone numbers. The search omits contacts matching query that also match any phone number in this set.

### `query`

iOS 18.0+

Optional • Type: `string`

A string to match against contacts not yet exposed to the app. You typically get this value from a search UI that your app presents, like a text field.

### `textColor`

iOS 18.0+

Optional • Type: `[ColorValue](https://reactnative.dev/docs/colors)`

A color of the button's title. Slightly dimmed version of this color is used for the caption text. Make sure there is a good contrast between the text and the background, otherwise platform requirements for button legibility may not be satisfied.

### `tintColor`

iOS 18.0+

Optional • Type: `[ColorValue](https://reactnative.dev/docs/colors)`

A tint color of the button and the modal that is presented when there is more than one match.

#### Inherited Props

  * `[ViewProps](https://reactnative.dev/docs/view#props)`


## Static Methods

### `isAvailable()`

Android

iOS

Returns a boolean whether the `ContactAccessButton` is available on the platform. This is `true` only on iOS 18.0 and newer.

Returns:

`boolean`

## Classes

### `Contact`

Android

iOS

Type: Class extends `Contact`

Represents a contact in the device's address book.

  * Data Retrieval: Contact details can be accessed using the `getDetails` method or via specific getters such as `getEmails` and `getPhones`.

  * Modification: To update the contact, use bulk operations via `patch` or `update`, or specific modifiers like `addEmail` and `deletePhone`.


Example


    const contact = await Contact.create({
       givenName: 'John',
       familyName: 'Doe',
       phones: [{ label: 'mobile', number: '+12123456789' }]
    });


Contact Properties

### `id`

Android

iOS

Type: `string`

The unique identifier for the contact.

  * For iOS it is the unique UUID string.
  * For Android it is the `_ID` column from `ContactsContract.Contacts` table.


Contact Methods

### `addAddress(address)`

Android

iOS

Parameter| Type| Description
---|---|---
address| `NewAddress`| The new address object to add.




Adds a new postal address to the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

a promise resolving to the ID of the newly added address.

Example


    await contact.addAddress({ label: 'home', street: '123 Main St', city: 'London' });


### `addDate(date)`

Android

iOS

Parameter| Type| Description
---|---|---
date| `NewDate`| The new date object to add.




Adds a new date (e.g., anniversary, birthday) to the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

a promise resolving to the ID of the newly added date.

Example


    await contact.addDate({ label: 'anniversary', date: { day: 1, month: 1 } });


### `addEmail(email)`

Android

iOS

Parameter| Type| Description
---|---|---
email| `NewEmail`| The new email object to add.




Adds a new email address to the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

a promise resolving to the ID of the newly added email.

Example


    const newEmailId = await contact.addEmail({ label: 'work', address: 'work@example.com' });


### `addExtraName(extraName)`

Android

Parameter| Type| Description
---|---|---
extraName| `NewExtraName`| The new extra name object to add.




Adds a new extra name (e.g., nickname, maiden name) to the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

a promise resolving to the ID of the newly added extra name.

Example


    await contact.addExtraName({ label: 'nickname', name: 'Johnny' });


### `addImAddress(imAddress)`

iOS

Parameter| Type| Description
---|---|---
imAddress| `NewImAddress`| The new IM address object to add.




Adds a new instant messaging address to the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

a promise resolving to the ID of the newly added IM address.

Example


    await contact.addImAddress({ service: 'Skype', username: 'user123' });


### `addPhone(phone)`

Android

iOS

Parameter| Type| Description
---|---|---
phone| `NewPhone`| The new phone object to add.




Adds a new phone number to the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

a promise resolving to the ID of the newly added phone number.

Example


    const newPhoneId = await contact.addPhone({ label: 'home', number: '+12123456789' });


### `addRelation(relation)`

Android

iOS

Parameter| Type| Description
---|---|---
relation| `NewRelation`| The new relation object to add.




Adds a new relationship (e.g., brother, sister) to the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

a promise resolving to the ID of the newly added relation.

Example


    await contact.addRelation({ label: 'brother', name: 'Mark' });


### `addSocialProfile(socialProfile)`

iOS

Parameter| Type| Description
---|---|---
socialProfile| `NewSocialProfile`| The new social profile object to add.




Adds a new social profile to the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

a promise resolving to the ID of the newly added social profile.

Example


    await contact.addSocialProfile({ service: 'twitter', username: 'myhandle' });


### `addUrlAddress(urlAddress)`

Android

iOS

Parameter| Type| Description
---|---|---
urlAddress| `NewUrlAddress`| The new URL address object to add.




Adds a new URL/website to the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

a promise resolving to the ID of the newly added URL.

Example


    await contact.addUrlAddress({ label: 'blog', url: '[https://myblog.com](https://myblog.com)' });


### `create(contact)`

Android

iOS

Parameter| Type| Description
---|---|---
contact| `CreateContactRecord`| The contact data to create.




A static method that creates a new contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Contact>`

a promise resolving to the newly created `Contact` instance.

Example


    const newContactDetails: CreateContactRecord = {
       givenName: 'Jane',
       familyName: 'Doe',
       phones: [{ label: 'mobile', number: '+12123456789' }]
    };
    const newContact = await Contact.create(newContactDetails);


### `delete()`

Android

iOS

Deletes the contact from the device's address book.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

a promise that resolves when the contact is successfully deleted.

Example


    await contact.delete();


### `deleteAddress(address)`

Android

iOS

Parameter| Type| Description
---|---|---
address| `ExistingAddress`| The existing address object to delete.




Deletes a specific postal address from the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    await contact.deleteAddress(existingAddress);


### `deleteDate(date)`

Android

iOS

Parameter| Type| Description
---|---|---
date| `ExistingDate`| The existing date object to delete.




Deletes a specific date from the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    await contact.deleteDate(existingDate);


### `deleteEmail(email)`

Android

iOS

Parameter| Type| Description
---|---|---
email| `ExistingEmail`| The existing email object to delete.




Deletes a specific email address from the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    await contact.deleteEmail(existingEmail);


### `deleteExtraName(extraName)`

Android

Parameter| Type| Description
---|---|---
extraName| `string | ExistingExtraName`| The existing extra name object or its ID string.




Deletes a specific extra name from the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    await contact.deleteExtraName(existingExtraName);


### `deleteImAddress(imAddress)`

iOS

Parameter| Type| Description
---|---|---
imAddress| `ExistingImAddress`| The existing IM address object to delete.




Deletes a specific instant messaging address from the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    await contact.deleteImAddress(existingImAddress);


### `deletePhone(phone)`

Android

iOS

Parameter| Type| Description
---|---|---
phone| `ExistingPhone`| The existing phone object to delete.




Deletes a specific phone number from the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    await contact.deletePhone(existingPhone);


### `deleteRelation(relation)`

Android

iOS

Parameter| Type| Description
---|---|---
relation| `string | ExistingRelation`| The existing relation object or its ID string.




Deletes a specific relation from the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    await contact.deleteRelation(existingRelation);


### `deleteSocialProfile(socialProfile)`

iOS

Parameter| Type| Description
---|---|---
socialProfile| `ExistingSocialProfile`| The existing social profile object to delete.




Deletes a specific social profile from the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    await contact.deleteSocialProfile(existingSocialProfile);


### `deleteUrlAddress(urlAddress)`

Android

iOS

Parameter| Type| Description
---|---|---
urlAddress| `ExistingUrlAddress`| The existing URL address object to delete.




Deletes a specific URL address from the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    await contact.deleteUrlAddress(existingUrlAddress);


### `editWithForm(options)`

Android

iOS

Parameter| Type| Description
---|---|---
options(optional)| `FormOptions`| Configuration options for the form.




Opens the native contact editor for this contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to `true` if changes were saved, `false` otherwise.

### `getAddresses()`

Android

iOS

Retrieves all postal addresses associated with the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingAddress[]>`

a promise resolving to an array of existing addresses.

Example


    const addresses = await contact.getAddresses();


### `getAll(options)`

Android

iOS

Parameter| Type| Description
---|---|---
options(optional)| `ContactQueryOptions`| Options to filter, sort, or limit the results.




A static method that retrieves all contacts from the address book.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Contact[]>`

a promise resolving to an array of `Contact` instances.

Example


    const contacts = await Contact.getAll({
      sort: ContactSortOrder.FirstName,
      limit: 10,
      offset: 0,
      name: 'John'
    });


### `getAllDetails(fields, options)`

Android

iOS

Parameter| Type| Description
---|---|---
fields| `T`| The list of fields to retrieve.
options(optional)| `ContactQueryOptions`| Query options to filter the contacts.




A static method that retrieves specific fields for all contacts or a subset of contacts. This is an optimized method for fetching bulk data; it avoids creating full `Contact` instances.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PartialContactDetails[]>`

a promise resolving to an array of partial contact details objects.

Example


    const allDetails = await Contact.getAllDetails(['givenName', 'phones'], {
      limit: 10,
      name: 'John'
    });


### `getBirthday()`

iOS

Retrieves the birthday of the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ContactDate | null>`

a promise resolving to the ContactDate object or `null` if not set.

Example


    const birthday = await contact.getBirthday();


### `getCompany()`

Android

iOS

Retrieves the company name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the company name string or `null` if not set.

Example


    const company = await contact.getCompany(); // 'Example Inc.'


### `getCount()`

Android

iOS

A static method that retrieves the total count of contacts in the address book.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<number>`

a promise resolving to the count of contacts.

Example


    const contactCount = await Contact.getCount(); // 42


### `getDates()`

Android

iOS

Retrieves all dates associated with the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingDate[]>`

a promise resolving to an array of existing dates.

Example


    const dates = await contact.getDates();


### `getDepartment()`

Android

iOS

Retrieves the department name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the department name string or `null` if not set.

Example


    const department = await contact.getDepartment(); // 'Sales'


### `getDetails(fields)`

Android

iOS

Parameter| Type| Description
---|---|---
fields(optional)| `T`| An array of field names to retrieve. If omitted, all available fields are fetched.




Retrieves specific details for the contact. This method is useful when you want to retrieve only certain fields of the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<PartialContactDetails<T>>`

a promise resolving to an object containing the requested details.

Example


    const details = await contact.getDetails([ContactField.GivenName, ContactField.Phones]);
    details.givenName; // 'John'
    details.familyName; // undefined
    details.phones; // [{ label: 'mobile', number: '+12123456789' }]


### `getEmails()`

Android

iOS

Retrieves all email addresses associated with the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingEmail[]>`

a promise resolving to an array of existing emails.

Example


    const emails = await contact.getEmails();


### `getExtraNames()`

Android

Retrieves all extra names associated with the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingExtraName[]>`

a promise resolving to an array of existing extra names.

Example


    const extraNames = await contact.getExtraNames();


### `getFamilyName()`

Android

iOS

Retrieves the family name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the family name string or `null` if not set.

Example


    const familyName = await contact.getFamilyName(); // 'Doe'


### `getFullName()`

Android

iOS

Retrieves the full name of the contact. The shape of the full name depends on the platform. This field is read-only and cannot be set directly. To modify name components, use the respective setters.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

a promise resolving to the full name string.

Example


    const fullName = await contact.getFullName(); // 'John Doe'


### `getGivenName()`

Android

iOS

Retrieves the given name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the given name string or `null` if not set.

Example


    const givenName = await contact.getGivenName(); // 'John'


### `getImAddresses()`

iOS

Retrieves all instant messaging addresses associated with the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingImAddress[]>`

a promise resolving to an array of existing IM addresses.

Example


    const ims = await contact.getImAddresses();


### `getImage()`

Android

iOS

Retrieves the URI of the contact's full-resolution image.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the image URI string or `null` if not set.

Example


    const imageUri = await contact.getImage();


### `getIsFavourite()`

Android

Retrieves whether the contact is marked as a favorite.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving boolean indicating whether the contact is a favorite.

Example


     const isFavourite = await contact.getIsFavourite() // true


### `getJobTitle()`

Android

iOS

Retrieves the job title.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the job title string or `null` if not set.

Example


    const jobTitle = await contact.getJobTitle(); // 'Software Engineer'


### `getMaidenName()`

iOS

Retrieves the maiden name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the maiden name string or `null` if not set.

Example


    const maidenName = await contact.getMaidenName();


### `getMiddleName()`

Android

iOS

Retrieves the middle name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the middle name string or `null` if not set.

Example


    const middleName = await contact.getMiddleName(); // 'Marie'


### `getNickname()`

iOS

Retrieves the nickname.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the nickname string or `null` if not set.

Example


    const nickname = await contact.getNickname(); // 'Johnny'


### `getNonGregorianBirthday()`

iOS

Retrieves the non-Gregorian birthday of the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<NonGregorianBirthday | null>`

a promise resolving to the NonGregorianBirthday object or `null` if not set.

Example


    const nonGregorianBirthday = await contact.getNonGregorianBirthday();


### `getNote()`

Android

iOS

Retrieves the note associated with the contact.

> On iOS the `note` field [requires your app to request additional entitlements](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_contacts_notes). The Expo Go app does not contain those entitlements, so in order to test this feature you will need to [request the entitlement from Apple](https://developer.apple.com/contact/request/contact-note-field), set the [`ios.accessesContactNotes`](/versions/latest/config/app#accessescontactnotes) field in app config to `true`, and [create your development build](/develop/development-builds/create-a-build).

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the note string or `null` if not set.

Example


    const note = await contact.getNote(); // 'Met at the conference'


### `getPhones()`

Android

iOS

Retrieves all phone numbers associated with the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingPhone[]>`

a promise resolving to an array of existing phone numbers.

Example


    const phones = await contact.getPhones();


### `getPhoneticCompanyName()`

Android

iOS

Retrieves the phonetic representation of the company name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the phonetic company name string or `null` if not set.

Example


    const phoneticCompanyName = await contact.getPhoneticCompanyName(); // 'Ekzampl Inc.'


### `getPhoneticFamilyName()`

Android

iOS

Retrieves the phonetic representation of the family name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the phonetic family name string or `null` if not set.

Example


    const phoneticFamilyName = await contact.getPhoneticFamilyName(); // 'Smyth'


### `getPhoneticGivenName()`

Android

iOS

Retrieves the phonetic representation of the given name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the phonetic given name string or `null` if not set.

Example


    const phoneticGivenName = await contact.getPhoneticGivenName(); // 'Jon'


### `getPhoneticMiddleName()`

Android

iOS

Retrieves the phonetic representation of the middle name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the phonetic middle name string or `null` if not set.

Example


    const phoneticMiddleName = await contact.getPhoneticMiddleName(); // 'Maree'


### `getPrefix()`

Android

iOS

Retrieves the name prefix.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the prefix string or `null` if not set.

Example


    const prefix = await contact.getPrefix(); // 'Dr.'


### `getRelations()`

Android

iOS

Retrieves all relations associated with the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingRelation[]>`

a promise resolving to an array of existing relations.

Example


    const relations = await contact.getRelations();


### `getSocialProfiles()`

iOS

Retrieves all social profiles associated with the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingSocialProfile[]>`

a promise resolving to an array of existing social profiles.

Example


    const profiles = await contact.getSocialProfiles();


### `getSuffix()`

Android

iOS

Retrieves the name suffix.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the suffix string or `null` if not set.

Example


    const suffix = await contact.getSuffix(); // 'Jr.'


### `getThumbnail()`

Android

iOS

Retrieves the URI of the contact's thumbnail image. This field is read-only and is derived from the full-resolution image.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the thumbnail URI string or `null` if not set.

Example


    const thumbnailUri = await contact.getThumbnail();


### `getUrlAddresses()`

Android

iOS

Retrieves all URL addresses associated with the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingUrlAddress[]>`

a promise resolving to an array of existing URL addresses.

Example


    const urls = await contact.getUrlAddresses();


### `hasAny()`

Android

iOS

A static method that checks if there are any contacts in the address book.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to `true` if at least one contact exists.

Example


    const hasContacts = await Contact.hasAny(); // true


### `patch(contact)`

Android

iOS

Parameter| Type| Description
---|---|---
contact| `ContactPatch`| A partial contact record containing the fields to update.




Applies partial updates to the contact. Undefined fields are ignored. Lists like `emails` or `phones` are entirely replaced if provided. If you want to overwrite the entire contact, use the `update` method instead.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const details = await contact.getDetails([ContactField.GivenName, ContactField.FamilyName, ContactField.Phones]);
    details.givenName = 'Jane'; // updates the given name
    details.familyName = null; // clears the family name
    details.phones = [
       ...details.phones,  // keeps existing phone numbers
       { label: 'newPhone', number: '+12123456789' } // adds a new phone number
    ];
    await contact.patch(details);


### `presentAccessPicker()`

iOS

A static method that presents a system dialog to request access to specific contacts (iOS 18+).

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Contact[]>`

a promise resolving to an array of `Contact` instances the user granted access to.

Example


    const contacts = await Contact.presentAccessPicker();


### `presentCreateForm(contact)`

Android

iOS

Parameter| Type| Description
---|---|---
contact(optional)| `CreateContactRecord`| Optional pre-filled data for the form.




A static method that opens the native "Create Contact" form.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to `true` if a contact was created, `false` otherwise.

Example


    const wasCreated = await Contact.createWithForm({
      givenName: 'Jane',
      familyName: 'Doe'
    });


### `presentPicker()`

Android

iOS

A static method that opens the native contact picker UI allowing the user to select a contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Contact | null>`

a promise resolving to the selected `Contact` instance, or `null` if the user cancelled.

Example


    const contact = await Contact.presentPicker();
    if (contact) {
      // user selected a contact
    }


### `setBirthday(birthday)`

iOS

Parameter| Type| Description
---|---|---
birthday| `ContactDate | null`| The new ContactDate object or `null` to clear it.




Sets the birthday of the contact. To set a birthday on Android, use the `addDate` method with the label 'birthday'.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setBirthday({ year: '1990', month: '1', day: '1' });


### `setCompany(company)`

Android

iOS

Parameter| Type| Description
---|---|---
company| `string | null`| The new company name string or `null` to clear it.




Sets the company name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setCompany('Example Inc.');


### `setDepartment(department)`

Android

iOS

Parameter| Type| Description
---|---|---
department| `string | null`| The new department name string or `null` to clear it.




Sets the department name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setDepartment('Sales');


### `setFamilyName(familyName)`

Android

iOS

Parameter| Type| Description
---|---|---
familyName| `string | null`| The new family name string or `null` to clear it.




Sets the family name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setFamilyName('Smith');


### `setGivenName(givenName)`

Android

iOS

Parameter| Type| Description
---|---|---
givenName| `string | null`| The new given name string or `null` to clear it.




Sets the given name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setGivenName('Jane');


### `setImage(imageUri)`

Android

iOS

Parameter| Type| Description
---|---|---
imageUri| `string | null`| The local file URI to the image or `null` to remove it.




Sets the contact's image.

> Note: If you have a remote URI, you have to download the image to a local file first.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setImage('file:///path/to/new/image.jpg');


### `setIsFavourite(isFavourite)`

Android

Parameter| Type| Description
---|---|---
isFavourite| `boolean`| a boolean indicating whether to mark the contact as a favorite.




Sets the favorite status of the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to boolean indicating whether the operation was successful.

Example


    await contact.setIsFavourite(true);


### `setJobTitle(jobTitle)`

Android

iOS

Parameter| Type| Description
---|---|---
jobTitle| `string | null`| The new job title string or `null` to clear it.




Sets the job title.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setJobTitle('Product Manager');


### `setMaidenName(maidenName)`

iOS

Parameter| Type| Description
---|---|---
maidenName| `string | null`| The new maiden name string or `null` to clear it.




Sets the maiden name. To set a maiden name on Android, use the `addExtraName` method with the label 'maidenname'.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setMaidenName('Johnson');


### `setMiddleName(middleName)`

Android

iOS

Parameter| Type| Description
---|---|---
middleName| `string | null`| The new middle name string or `null` to clear it.




Sets the middle name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setMiddleName('Lee');


### `setNickname(nickname)`

iOS

Parameter| Type| Description
---|---|---
nickname| `string | null`| The new nickname string or `null` to clear it.




Sets the nickname. To set a nickname on Android, use the `addExtraName` method with the label 'nickname'.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setNickname('Jojo');


### `setNonGregorianBirthday(nonGregorianBirthday)`

iOS

Parameter| Type| Description
---|---|---
nonGregorianBirthday| `NonGregorianBirthday | null`| The new NonGregorianBirthday object or `null` to clear it.




Sets the non-Gregorian birthday of the contact.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setNonGregorianBirthday({
       year: '2563',
       month: '5',
       day: '15',
       calendar: NonGregorianCalendar.buddhist
    });


### `setNote(note)`

Android

iOS

Parameter| Type| Description
---|---|---
note| `string | null`| The new note string or `null` to clear it.




Sets the note for the contact.

> On iOS the `note` field [requires your app to request additional entitlements](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_contacts_notes). The Expo Go app does not contain those entitlements, so in order to test this feature you will need to [request the entitlement from Apple](https://developer.apple.com/contact/request/contact-note-field), set the [`ios.accessesContactNotes`](/versions/latest/config/app#accessescontactnotes) field in app config to `true`, and [create your development build](/develop/development-builds/create-a-build).

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setNote('Remember to call back');


### `setPhoneticCompanyName(phoneticCompanyName)`

Android

iOS

Parameter| Type| Description
---|---|---
phoneticCompanyName| `string | null`| The new phonetic company name string or `null` to clear it.




Sets the phonetic company name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setPhoneticCompanyName('Ekzampl Inc.');


### `setPhoneticFamilyName(phoneticFamilyName)`

Android

iOS

Parameter| Type| Description
---|---|---
phoneticFamilyName| `string | null`| The new phonetic family name string or `null` to clear it.




Sets the phonetic family name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setPhoneticFamilyName('Smyth');


### `setPhoneticGivenName(phoneticGivenName)`

Android

iOS

Parameter| Type| Description
---|---|---
phoneticGivenName| `string | null`| The new phonetic given name string or `null` to clear it.




Sets the phonetic given name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setPhoneticGivenName('Jon');


### `setPhoneticMiddleName(phoneticMiddleName)`

Android

iOS

Parameter| Type| Description
---|---|---
phoneticMiddleName| `string | null`| The new phonetic middle name string or `null` to clear it.




Sets the phonetic middle name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setPhoneticMiddleName('Maree');


### `setPrefix(prefix)`

Android

iOS

Parameter| Type| Description
---|---|---
prefix| `string | null`| The new prefix string or `null` to clear it.




Sets the name prefix.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setPrefix('Ms.');


### `setSuffix(suffix)`

Android

iOS

Parameter| Type| Description
---|---|---
suffix| `string | null`| The new suffix string or `null` to clear it.




Sets the name suffix.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

a promise resolving to a boolean indicating whether the operation was successful.

Example


    await contact.setSuffix('Jr.');


### `update(contact)`

Android

iOS

Parameter| Type| Description
---|---|---
contact| `CreateContactRecord`| The new data record for the contact.




Overwrites the contact data with the provided record. If you want to apply partial updates, use the `patch` method instead.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const newDetails: CreateContactRecord = {
       givenName: 'Jane',
       familyName: 'Doe',
       phones: [{ label: 'mobile', number: '+12123456789' }]
    };
    await contact.update(newDetails);


### `updateAddress(updatedAddress)`

Android

iOS

Parameter| Type| Description
---|---|---
updatedAddress| `ExistingAddress`| The address object with updated values. Must contain a valid ID.




Updates an existing postal address.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const addresses = await contact.getAddresses();
    const addressToUpdate = addresses[0];
    addressToUpdate.city = 'New York';
    await contact.updateAddress(addressToUpdate);


### `updateDate(updatedDate)`

Android

iOS

Parameter| Type| Description
---|---|---
updatedDate| `ExistingDate`| The date object with updated values. Must contain a valid ID.




Updates an existing date.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const dates = await contact.getDates();
    const dateToUpdate = dates[0];
    dateToUpdate.label = 'birthday';
    await contact.updateDate(dateToUpdate);


### `updateEmail(updatedEmail)`

Android

iOS

Parameter| Type| Description
---|---|---
updatedEmail| `ExistingEmail`| The email object with updated values. Must contain a valid ID.




Updates an existing email address.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const emails = await contact.getEmails();
    const emailToUpdate = emails[0];
    emailToUpdate.address = 'new@example.com';
    await contact.updateEmail(emailToUpdate);


### `updateExtraName(updatedExtraName)`

Android

Parameter| Type| Description
---|---|---
updatedExtraName| `ExistingExtraName`| The extra name object with updated values. Must contain a valid ID.




Updates an existing extra name.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const names = await contact.getExtraNames();
    const nameToUpdate = names[0];
    nameToUpdate.name = 'Jojo';
    await contact.updateExtraName(nameToUpdate);


### `updateImAddress(updatedImAddress)`

iOS

Parameter| Type| Description
---|---|---
updatedImAddress| `ExistingImAddress`| The IM address object with updated values. Must contain a valid ID.




Updates an existing instant messaging address.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const ims = await contact.getImAddresses();
    const imToUpdate = ims[0];
    imToUpdate.username = 'user456';
    await contact.updateImAddress(imToUpdate);


### `updatePhone(updatedPhone)`

Android

iOS

Parameter| Type| Description
---|---|---
updatedPhone| `ExistingPhone`| The phone object with updated values. Must contain a valid ID.




Updates an existing phone number.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const phones = await contact.getPhones();
    const phoneToUpdate = phones[0];
    phoneToUpdate.number = '+19876543210';
    await contact.updatePhone(phoneToUpdate);


### `updateRelation(updatedRelation)`

Android

iOS

Parameter| Type| Description
---|---|---
updatedRelation| `ExistingRelation`| The relation object with updated values. Must contain a valid ID.




Updates an existing relation.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const relations = await contact.getRelations();
    const relationToUpdate = relations[0];
    relationToUpdate.name = 'Marcus';
    await contact.updateRelation(relationToUpdate);


### `updateSocialProfile(updatedSocialProfile)`

iOS

Parameter| Type| Description
---|---|---
updatedSocialProfile| `ExistingSocialProfile`| The social profile object with updated values. Must contain a valid ID.




Updates an existing social profile.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const profiles = await contact.getSocialProfiles();
    const profileToUpdate = profiles[0];
    profileToUpdate.username = 'newhandle';
    await contact.updateSocialProfile(profileToUpdate);


### `updateUrlAddress(updatedUrlAddress)`

Android

iOS

Parameter| Type| Description
---|---|---
updatedUrlAddress| `ExistingUrlAddress`| The URL address object with updated values. Must contain a valid ID.




Updates an existing URL address.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

Example


    const urls = await contact.getUrlAddresses();
    const urlToUpdate = urls[0];
    urlToUpdate.url = '[https://updated-blog.com](https://updated-blog.com)';
    await contact.updateUrlAddress(urlToUpdate);


### `Container`

iOS

Type: Class extends `Container<this>`

Represents a container for contacts. A container (often called an "Account" in UI terms) is a source of contacts, such as a local device storage, iCloud, Google, or Exchange account.

Container Properties

### `id`

iOS

Read Only • Type: `string`

The unique identifier for the container.

Container Methods

### `getAll()`

iOS

A static method that retrieves all contact containers available on the device.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Container[]>`

a promise resolving to an array of `Container` instances.

Example


    const containers = await Container.getAll();


### `getContacts()`

iOS

Retrieves all contacts stored in this container.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Contact[]>`

a promise resolving to an array of `Contact` instances within this container.

Example


    const contacts = await container.getContacts();


### `getDefault()`

iOS

A static method that retrieves the default container. The default container is where new contacts are added if no specific container is specified.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Container | null>`

a promise resolving to the default `Container` instance or `null` if not found.

Example


    const defaultContainer = await Container.getDefault();


### `getGroups()`

iOS

Retrieves all groups associated with this container.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Group[]>`

a promise resolving to an array of `Group` instances within this container.

Example


    const groups = await container.getGroups();


### `getName()`

iOS

Retrieves the name of the container.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the container name string (for example, "iCloud", "Gmail") or `null` if not available.

Example


    const name = await container.getName(); // 'iCloud'


### `getType()`

iOS

Retrieves the type of the container.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ContainerType | null>`

a promise resolving to the `ContainerType` (for example, 'cardDAV', 'exchange') or `null`.

Example


    const type = await container.getType(); // 'cardDAV'


### `Group`

iOS

Type: Class extends `Group<this>`

Represents a group of contacts (for example, "Family", "Coworkers"). Groups belong to a specific Container and can contain multiple Contacts.

Group Properties

### `id`

iOS

Read Only • Type: `string`

The unique identifier for the group.

Group Methods

### `addContact(contact)`

iOS

Parameter| Type| Description
---|---|---
contact| `Contact`| The `Contact` instance to add to the group.




Adds a contact to the group.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

a promise that resolves when the contact is successfully added.

Example


    await group.addContact(contact);


### `create(name, containerId)`

iOS

Parameter| Type| Description
---|---|---
name| `string`| The name of the new group.
containerId(optional)| `string`| The ID of the container where the group should be created. If omitted, the default container is used.




A static method that creates a new group.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Group>`

a promise resolving to the newly created `Group` instance.

Example


    const newGroup = await Group.create('Gym Buddies');


### `delete()`

iOS

Deletes the group from the device.

> Note: This usually deletes the group definition but leaves the contacts themselves intact in the address book.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

a promise that resolves when the group is successfully deleted.

Example


    await group.delete();


### `getAll(containerId)`

iOS

Parameter| Type| Description
---|---|---
containerId(optional)| `string`| Optional ID of a container to filter groups by. If omitted, groups from all containers are returned.




A static method that retrieves all groups.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Group[]>`

a promise resolving to an array of `Group` instances.

Example


    const allGroups = await Group.getAll();


### `getContacts(options)`

iOS

Parameter| Type| Description
---|---|---
options(optional)| `ContactQueryOptions`| Options to filter, sort, or limit the results.




Retrieves contacts belonging to this group.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Contact[]>`

a promise resolving to an array of `Contact` instances in this group.

Example


    const groupMembers = await group.getContacts({ sort: 'firstName' });


### `getName()`

iOS

Retrieves the name of the group.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | null>`

a promise resolving to the group name string or `null` if not set.

Example


    const name = await group.getName(); // 'Family'


### `removeContact(contact)`

iOS

Parameter| Type| Description
---|---|---
contact| `Contact`| The `Contact` instance to remove from the group.




Removes a contact from the group.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

a promise that resolves when the contact is successfully removed.

Example


    await group.removeContact(contact);


### `setName(name)`

iOS

Parameter| Type| Description
---|---|---
name| `string`| The new name for the group.




Renames the group.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<void>`

a promise that resolves when the group is successfully renamed.

Example


    await group.setName('Close Friends');


## Methods

> Deprecated: Use `Contact.create()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.addContactAsync(contact, containerId)`

Android

iOS

Parameter| Type| Description
---|---|---
contact| `Contact`| A contact with the changes you wish to persist. The `id` parameter will not be used.
containerId(optional)| `string`| -




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `group.addContact()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.addExistingContactToGroupAsync(contactId, groupId)`

Android

iOS

Parameter| Type| Description
---|---|---
contactId| `string`| ID of the contact you want to edit.
groupId| `string`| ID for the group you want to add membership to.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<any>`

> Deprecated: Use `container.addGroup()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.addExistingGroupToContainerAsync(groupId, containerId)`

Android

iOS

Parameter| Type| Description
---|---|---
groupId| `string`| The group you want to target.
containerId| `string`| The container you want to add membership to.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<any>`

> Deprecated: Use `Group.create()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.createGroupAsync(name, containerId)`

Android

iOS

Parameter| Type| Description
---|---|---
name(optional)| `string`| Name of the new group.
containerId(optional)| `string`| The container you to add membership to.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `new Contact(id).getDetails()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.getContactByIdAsync(id, fields)`

Android

iOS

Parameter| Type| Description
---|---|---
id| `string`| The ID of a system contact.
fields(optional)| `FieldType[]`| If specified, the fields defined will be returned. When skipped, all fields will be returned.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingContact | undefined>`

> Deprecated: Use `Contact.getAll()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.getContactsAsync(contactQuery)`

Android

iOS

Parameter| Type| Description
---|---|---
contactQuery(optional)| `ContactQuery`| Object used to query contacts.Default:`{}`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ContactResponse>`

> Deprecated: Use `Container.getAll()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.getContainersAsync(containerQuery)`

Android

iOS

Parameter| Type| Description
---|---|---
containerQuery| `ContainerQuery`| Information used to gather containers.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Container[]>`

> Deprecated: Use `Container.getDefault()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.getDefaultContainerIdAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `Group.getAll()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.getGroupsAsync(groupQuery)`

Android

iOS

Parameter| Type| Description
---|---|---
groupQuery| `GroupQuery`| Information regarding which groups you want to get.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<Group[]>`

> Deprecated: Use `Contact.getAll()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.getPagedContactsAsync(contactQuery)`

Android

iOS

Parameter| Type
---|---
contactQuery(optional)| `ContactQuery`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ContactResponse>`

### `Contacts.getPermissionsAsync()`

Android

iOS

Checks user's permissions for accessing contacts data.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ContactsPermissionResponse>`

A promise that resolves to a `ContactsPermissionResponse` object.

> Deprecated: Use `Contact.hasAny()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.hasContactsAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

> Deprecated: Import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.isAvailableAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<boolean>`

> Deprecated: Use `Contact.presentAccessPicker()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.presentAccessPickerAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string[]>`

> Deprecated: Use `Contact.presentPicker()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.presentContactPickerAsync()`

Android

iOS

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ExistingContact | null>`

> Deprecated: Use `contact.editWithForm()` or `Contact.presentCreateForm()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.presentFormAsync(contactId, contact, formOptions)`

Android

iOS

Parameter| Type| Description
---|---|---
contactId(optional)| `string | null`| The ID of a system contact.
contact(optional)| `Contact | null`| A contact with the changes you want to persist.
formOptions(optional)| `FormOptions`| Options for the native editor.Default:`{}`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<any>`

> Deprecated: Use `contact.delete()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.removeContactAsync(contactId)`

Android

iOS

Parameter| Type| Description
---|---|---
contactId| `string`| ID of the contact you want to delete.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<any>`

> Deprecated: Use `group.removeContact()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.removeContactFromGroupAsync(contactId, groupId)`

Android

iOS

Parameter| Type| Description
---|---|---
contactId| `string`| ID of the contact you want to remove.
groupId| `string`| ID for the group you want to remove membership of.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<any>`

> Deprecated: Use `group.delete()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.removeGroupAsync(groupId)`

Android

iOS

Parameter| Type| Description
---|---|---
groupId| `string`| ID of the group you want to remove.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<any>`

### `Contacts.requestPermissionsAsync()`

Android

iOS

Asks the user to grant permissions for accessing contacts data.

Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<ContactsPermissionResponse>`

A promise that resolves to a `ContactsPermissionResponse` object.

> Deprecated: Import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.shareContactAsync(contactId, message, shareOptions)`

Android

iOS

Parameter| Type
---|---
contactId| `string`
message| `string`
shareOptions(optional)| `[ShareOptions](https://reactnative.dev/docs/share#share)`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<any>`

> Deprecated: Use `contact.update()` or `contact.patch()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.updateContactAsync(contact)`

Android

iOS

Parameter| Type| Description
---|---|---
contact| `{  id: string } & [Partial](https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype)<ExistingContact>`| A contact object including the wanted changes. Contact `id` is required.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string>`

> Deprecated: Use `group.setName()` or import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.updateGroupNameAsync(groupName, groupId)`

Android

iOS

Parameter| Type| Description
---|---|---
groupName| `string`| New name for an existing group.
groupId| `string`| ID of the group you want to edit.




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<any>`

> Deprecated: Import this method from `expo-contacts/legacy`. This method will throw in runtime.

### `Contacts.writeContactToFileAsync(contactQuery)`

Android

iOS

Parameter| Type| Description
---|---|---
contactQuery(optional)| `ContactQuery`| Used to query contact you want to write.Default:`{}`




Returns:

`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)<string | undefined>`

## Event Subscriptions

### `Contacts.addContactsChangeListener(listener)`

Android

iOS

Parameter| Type| Description
---|---|---
listener| `() => void`| A callback invoked when contacts change. The callback receives no arguments.




Adds a listener that is called when contacts are added, updated, or deleted.

Platform differences:

  * Android: Uses `ContentObserver`, which may delay notifications by 5-7 seconds. Because it observes both `RawContacts` and `Contacts`, some changes may emit two events.
  * iOS: Uses `CNContactStoreDidChangeNotification` and emits updates immediately.


On Android, the delay comes from the system contact provider batching notifications for performance and battery life. If your app needs fresher data after returning from the native Contacts app, consider refreshing contacts when the app comes back to the foreground.

Returns:

`EventSubscription`

A subscription object with a `remove` method that stops listening for changes.

Example


    const subscription = Contacts.addContactChangeListener(() => {
      console.log('Contacts changed - refreshing contact list');
      loadContacts();
    });

    subscription.remove();


### `Contacts.removeAllContactsChangeListeners()`

Android

iOS

Removes all contact change listeners registered with `addContactsChangeListener`.

Returns:

`void`

## Types

### `ContactDate`

Android

iOS

Contact date representation.

Property| Type| Description
---|---|---
day| `number`| Day component of the date in format 1-31.
month| `number`| Month component of the date in format 1-12.
year(optional)| `number`| Year component of the date. For birthday dates, this field can be omitted to represent a date without a year.

### `ContactDetails`

Android

iOS

Represents the full details of an existing contact. This object is returned by `Contact.getContact` or similar methods.

Property| Type| Description
---|---|---
addresses| `ExistingAddress[]`| List of existing postal addresses associated with the contact.
birthday(optional)| `ContactDate | null`| Only for: iOS
Birthday of the contact.
company| `string | null`| Company name of the contact.Example`"Expo"`
dates| `ExistingDate[]`| List of existing dates associated with the contact.
department| `string | null`| Department of the contact.Example`"Engineering"`
emails| `ExistingEmail[]`| List of existing emails associated with the contact.
extraNames| `ExistingExtraName[]`| Only for: Android
List of existing extra names associated with the contact.
familyName| `string | null`| Family name (last name) of the contact.Example`"Smith"`
fullName| `string | null`| The composite full name of the contact.Example`"Dr. John Andrew Smith Jr."`
givenName| `string | null`| Given name of the contact.Example`"John"`
imAddresses| `ExistingImAddress[]`| Only for: iOS
List of existing instant messaging addresses associated with the contact.
image| `string | null`| URI of the contact's image.Example`"file:///path/to/image.jpg"`
isFavourite| `boolean`| Only for: Android
Whether the contact is marked as a favorite.
jobTitle(optional)| `string`| Job title of the contact.Example`"Software Engineer"`
maidenName(optional)| `string | null`| Only for: iOS
Maiden name of the contact.Example`"Johnson"`
middleName| `string | null`| Middle name of the contact.Example`"Andrew"`
nickname(optional)| `string | null`| Only for: iOS
Nickname of the contact.Example`"Johnny"`
nonGregorianBirthday(optional)| `NonGregorianBirthday | null`| Only for: iOS
Non-Gregorian birthday of the contact.
note| `string | null`| Note associated with the contact.Example`"Met at the conference."`
phones| `ExistingPhone[]`| List of existing phone numbers associated with the contact.
phoneticCompanyName(optional)| `string`| Phonetic company name of the contact.Example`"Ekspo"`
phoneticFamilyName| `string | null`| Phonetic family name of the contact.Example`"Smith"`
phoneticGivenName| `string | null`| Phonetic given name of the contact.Example`"Jon"`
phoneticMiddleName| `string | null`| Phonetic middle name of the contact.Example`"Andrews"`
prefix| `string | null`| Prefix (title) of the contact.Example`"Dr."`
relations| `ExistingRelation[]`| List of existing relations associated with the contact.
socialProfiles| `ExistingSocialProfile[]`| List of existing social profiles associated with the contact.
suffix| `string | null`| Suffix of the contact.Example`"Jr."`
thumbnail| `string | null`| URI of the contact's thumbnail.Example`"file:///path/to/image.jpg"`
urlAddresses| `ExistingUrlAddress[]`| List of existing URL addresses associated with the contact.

### `ContactFieldKey`

Android

iOS

Property| Type| Description
---|---|---
addresses| `'addresses'`| -
birthday| `'birthday'`| -
company| `'company'`| -
dates| `'dates'`| -
department| `'department'`| -
emails| `'emails'`| -
extraNames| `'extraNames'`| -
familyName| `'familyName'`| -
fullName| `'fullName'`| -
givenName| `'givenName'`| -
imAddresses| `'imAddresses'`| -
image| `'image'`| -
isFavourite| `'isFavourite'`| -
jobTitle| `'jobTitle'`| -
maidenName| `'maidenName'`| -
middleName| `'middleName'`| -
nickname| `'nickname'`| -
nonGregorianBirthday| `'nonGregorianBirthday'`| -
note| `'note'`| -
phones| `'phones'`| -
phoneticCompanyName| `'phoneticCompanyName'`| -
phoneticFamilyName| `'phoneticFamilyName'`| -
phoneticGivenName| `'phoneticGivenName'`| -
phoneticMiddleName| `'phoneticMiddleName'`| -
prefix| `'prefix'`| -
relations| `'relations'`| -
socialProfiles| `'socialProfiles'`| -
suffix| `'suffix'`| -
thumbnail| `'thumbnail'`| -
urlAddresses| `'urlAddresses'`| -

### `ContactPatch`

Android

iOS

Represents a set of fields to be patched on a contact. Undefined fields will be ignored. To remove a value, set the field to null.

Property| Type| Description
---|---|---
addresses(optional)| `(ExistingAddress | NewAddress)[]`| Addresses associated with the contact. It can be a mix of existing and new addresses. Existing addresses will be updated; new addresses will be added; not present addresses will be deleted.
birthday(optional)| `ContactDate | null`| Only for: iOS
Birthday of the contact.
company(optional)| `string | null`| Company name of the contact.Example`"Expo"`
dates(optional)| `(ExistingDate | NewDate)[]`| Dates associated with the contact. It can be a mix of existing and new dates. Existing dates will be updated; new dates will be added; not present dates will be deleted.
department(optional)| `string | null`| Department of the contact.Example`"Engineering"`
emails(optional)| `(ExistingEmail | NewEmail)[]`| Emails associated with the contact. It can be a mix of existing and new emails. Existing emails will be updated; new emails will be added; not present emails will be deleted.
extraNames(optional)| `(ExistingExtraName | NewExtraName)[]`| Only for: Android
Extra names associated with the contact. It can be a mix of existing and new extra names. Existing extra names will be updated; new extra names will be added; not present extraNames will be deleted.
familyName(optional)| `string | null`| Family name (last name) of the contact.Example`"Smith"`
givenName(optional)| `string | null`| Given name of the contact.Example`"John"`
imAddresses(optional)| `(ExistingImAddress | NewImAddress)[]`| Only for: iOS
Instant messaging addresses associated with the contact. It can be a mix of existing and new instant messaging addresses. Existing instant messaging addresses will be updated, new instant messaging addresses will be added; not present imAddresses will be deleted.
image(optional)| `string | null`| URI of the contact's image. Network URLs are not supported.Example`"file:///path/to/image.jpg"`
isFavourite(optional)| `boolean | null`| Only for: Android
Whether the contact is marked as a favorite.Example`true`
jobTitle(optional)| `string | null`| Job title of the contact.Example`"Software Engineer"`
maidenName(optional)| `string | null`| Only for: iOS
Maiden name of the contact.Example`"Johnson"`
middleName(optional)| `string | null`| Middle name of the contact.Example`"Andrew"`
nickname(optional)| `string | null`| Only for: iOS
Nickname of the contact.Example`"Johnny"`
nonGregorianBirthday(optional)| `NonGregorianBirthday | null`| Only for: iOS
Non-Gregorian birthday of the contact.
note(optional)| `string | null`| Note associated with the contact.Example`"Met at the conference."`
phones(optional)| `(ExistingPhone | NewPhone)[]`| Phone numbers associated with the contact. It can be a mix of existing and new phone numbers. Existing phone numbers will be updated; new phone numbers will be added; not present phone numbers will be deleted.
phoneticCompanyName(optional)| `string | null`| Phonetic company name of the contact.Example`"Ekspo"`
phoneticFamilyName(optional)| `string | null`| Phonetic family name of the contact.Example`"Smith"`
phoneticGivenName(optional)| `string | null`| Phonetic given name of the contact.Example`"Jon"`
phoneticMiddleName(optional)| `string | null`| Phonetic middle name of the contact.Example`"Andrews"`
prefix(optional)| `string | null`| Prefix (title) of the contact.Example`"Dr."`
relations(optional)| `(ExistingRelation | NewRelation)[]`| Relations associated with the contact. It can be a mix of existing and new relations. Existing relations will be updated; new relations will be added; not present relations will be deleted.
socialProfiles(optional)| `(ExistingSocialProfile | NewSocialProfile)[]`| Only for: iOS
Social profiles associated with the contact. It can be a mix of existing and new social profiles. Existing social profiles will be updated; new social profiles will be added; not present socialProfiles will be deleted.
suffix(optional)| `string | null`| Suffix of the contact.Example`"Jr."`
urlAddresses(optional)| `(ExistingUrlAddress | NewUrlAddress)[]`| URL addresses associated with the contact. It can be a mix of existing and new URL addresses. Existing URL addresses will be updated; new URL addresses will be added; not present urlAddresses will be deleted.

### `ContactQueryOptions`

Android

iOS

Options for querying multiple contacts.

Property| Type| Description
---|---|---
limit(optional)| `number`| Maximum number of contacts to return. If not specified, all matching contacts are returned.
name(optional)| `string`| A string to filter contacts by name. If specified, only contacts whose name contains this string are returned.
offset(optional)| `number`| Number of contacts to skip from the start of the result set. If not specified, starts from the beginning.
rawContacts(optional)| `boolean`| Only for: iOS
Whether to include raw contact data in the results. Default is false.
sortOrder(optional)| `ContactsSortOrder`| Sort order for the returned contacts. If not specified, the default sort order is used.

### `ContactsPermissionResponse`

Android

iOS

Type: `PermissionResponse` extended by:

Property| Type| Description
---|---|---
accessPrivileges(optional)| `'all' | 'limited' | 'none'`| Indicates if your app has access to the whole or only part of the contact library. Possible values are:

  * `'all'` if the user granted your app access to the whole contact library
  * `'limited'` if the user granted your app access only to selected contacts (only available on iOS 18+)
  * `'none'`



### `CreateContactRecord`

Android

iOS

Represents the data required to create a new contact.

Property| Type| Description
---|---|---
addresses(optional)| `NewAddress[]`| List of new postal addresses to be added to the contact.
birthday(optional)| `ContactDate`| Only for: iOS
Birthday of the contact.
company(optional)| `string`| Company name of the contact.Example`"Expo"`
dates(optional)| `NewDate[]`| List of new dates to be added to the contact.
department(optional)| `string`| Department of the contact.Example`"Engineering"`
emails(optional)| `NewEmail[]`| List of new emails to be added to the contact.
extraNames(optional)| `NewExtraName[]`| Only for: Android
List of new extra names to be added to the contact.
familyName(optional)| `string`| Family name (last name) of the contact.Example`"Smith"`
givenName(optional)| `string`| Given name of the contact.Example`"John"`
imAddresses(optional)| `NewImAddress[]`| Only for: iOS
List of new instant messaging addresses to be added to the contact.
image(optional)| `string`| URI of the contact's image. Network URLs are not supported.Example`"file:///path/to/image.jpg"`
isFavourite(optional)| `boolean`| Only for: Android
Whether the contact is marked as a favorite.Example`true`
jobTitle(optional)| `string`| Job title of the contact.Example`"Software Engineer"`
maidenName(optional)| `string`| Only for: iOS
Maiden name of the contact.Example`"Johnson"`
middleName(optional)| `string`| Middle name of the contact.Example`"Andrew"`
nickname(optional)| `string`| Only for: iOS
Nickname of the contact.Example`"Johnny"`
nonGregorianBirthday(optional)| `NonGregorianBirthday`| Only for: iOS
Non-Gregorian birthday of the contact.
note(optional)| `string`| Note associated with the contact.Example`"Met at the conference."`
phones(optional)| `NewPhone[]`| List of new phone numbers to be added to the contact.
phoneticCompanyName(optional)| `string`| Phonetic company name of the contact.Example`"Ekspo"`
phoneticFamilyName(optional)| `string`| Phonetic family name of the contact.Example`"Smith"`
phoneticGivenName(optional)| `string`| Phonetic given name of the contact.Example`"Jon"`
phoneticMiddleName(optional)| `string`| Phonetic middle name of the contact.Example`"Andrews"`
prefix(optional)| `string`| Prefix (title) of the contact.Example`"Dr."`
relations(optional)| `NewRelation[]`| List of new relations to be added to the contact.
socialProfiles(optional)| `NewSocialProfile[]`| List of new social profiles to be added to the contact.
suffix(optional)| `string`| Suffix of the contact.Example`"Jr."`
urlAddresses(optional)| `NewUrlAddress[]`| List of new URL addresses to be added to the contact.

### `ExistingAddress`

Android

iOS

Represents an existing postal address associated with a contact.

Type: `NewAddress` extended by:

Property| Type| Description
---|---|---
id| `string`| Unique identifier for the existing address. For iOS its an identifier from CNLabeledValue, for Android it's the _ID column from ContactsContract.CommonDataKinds.StructuredPostal table.

### `ExistingDate`

Android

iOS

Represents an existing date associated with a contact.

Type: `NewDate` extended by:

Property| Type| Description
---|---|---
id| `string`| Unique identifier for the existing date. For iOS its an identifier from CNLabeledValue, for Android it's the _ID column from ContactsContract.CommonDataKinds.Date table.

### `ExistingEmail`

Android

iOS

Represents an existing email associated with a contact. This object can be obtained from `Contact.getEmails` or 'contact.getDetails' methods.

Type: `NewEmail` extended by:

Property| Type| Description
---|---|---
id| `string`| Unique identifier for the existing email. It is generated by the system when an email is added. For iOS its an identifier from CNLabeledValue, for Android it's the _ID column from ContactsContract.CommonDataKinds.Email table.

### `ExistingExtraName`

Android

Represents an existing extra name associated with a contact. This object can be obtained from `Contact.getExtraNames` or 'contact.getDetails' methods.

Type: `NewExtraName` extended by:

Property| Type| Description
---|---|---
id| `string`| Unique identifier for the existing extra name. This value is generated by the system. It's the _ID column from ContactsContract.CommonDataKinds.Nickname table.

### `ExistingImAddress`

iOS

Represents an existing instant messaging address associated with a contact.

Type: `NewImAddress` extended by:

Property| Type| Description
---|---|---
id| `string`| Unique identifier for the existing instant messaging address. Its an identifier from CNLabeledValue.

### `ExistingPhone`

Android

iOS

Represents an existing phone number associated with a contact.

Type: `NewPhone` extended by:

Property| Type| Description
---|---|---
id| `string`| Unique identifier for the existing phone. For iOS its an identifier from CNLabeledValue, for Android it's the _ID column from ContactsContract.CommonDataKinds.Phone table.

### `ExistingRelation`

Android

iOS

Represents an existing relation associated with a contact.

Type: `NewRelation` extended by:

Property| Type| Description
---|---|---
id| `string`| Unique identifier for the existing relation. For iOS its an identifier from CNLabeledValue, for Android it's the _ID column from ContactsContract.CommonDataKinds.Relation table.

### `ExistingSocialProfile`

Android

iOS

Represents an existing social profile associated with a contact.

Type: `NewSocialProfile` extended by:

Property| Type| Description
---|---|---
id| `string`| Unique identifier for the existing social profile. Its an identifier from CNLabeledValue.

### `ExistingUrlAddress`

Android

iOS

Represents an existing URL address associated with a contact.

Type: `NewUrlAddress` extended by:

Property| Type| Description
---|---|---
id| `string`| Unique identifier for the existing URL address. For iOS its an identifier from CNLabeledValue, for Android it's the _ID column from ContactsContract.CommonDataKinds.Website table.

### `FormOptions`

iOS

Denotes the functionality of a native contact form.

Property| Type| Description
---|---|---
allowsActions(optional)| `boolean`| Actions like share, add, create.
allowsEditing(optional)| `boolean`| Allows for contact mutation.
alternateName(optional)| `string`| Used if contact doesn't have a name defined.
cancelButtonTitle(optional)| `string`| The name of the left bar button. Only applies when editing an existing contact.
displayedPropertyKeys(optional)| `ContactField[]`| The properties that will be displayed when viewing a contact.
groupId(optional)| `string`| The parent group for a new contact.
isNew(optional)| `boolean`| Present the new contact controller. If set to `false` the unknown controller will be shown.
message(optional)| `string`| The message displayed under the name of the contact. Only applies when editing an existing contact.
preventAnimation(optional)| `boolean`| Prevents the controller from animating in.
shouldShowLinkedContacts(optional)| `boolean`| Show or hide the similar contacts.

### `NewAddress`

Android

iOS

Represents a new postal address to be added to a contact.

Property| Type| Description
---|---|---
city(optional)| `string`| Can be city, town village etc.Example`"San Francisco"`
country(optional)| `string`| Country name.Example`"USA"`
label(optional)| `string`| Label associated with the address. If not provided, a label "other" will be used.Example`"work", "home"`
postcode(optional)| `string`| Postal code.Example`"94105"`
region(optional)| `string`| Region name.Example`"California"`
state(optional)| `string`| A state, province, county (in Ireland), Land (in Germany), departement (in France), etc.Example`"CA"`
street(optional)| `string`| Can be street, avenue road etc. This element also includes the house number and apartment/suite number if applicable.Example`"123 Main St"`

### `NewDate`

Android

iOS

Represents a date associated with a contact.

Property| Type| Description
---|---|---
date(optional)| `ContactDate`| Represents the date.
label(optional)| `string`| Label associated with the date. If not provided, a label "other" will be used.Example`"anniversary", "birthday"`

### `NewEmail`

Android

iOS

Represents a new email to be added to a contact.

Property| Type| Description
---|---|---
address(optional)| `string`| Represents the email address.Example`"user@example.com"`
label(optional)| `string`| Label associated with the email. If not provided, a label "other" will be used.Example`"work", "home"`

### `NewExtraName`

Android

Represents a new extra name to be added to a contact.

Property| Type| Description
---|---|---
label(optional)| `string`| Label associated with the extra name. If not provided, a label "other" will be used.Example`"maiden", "nickname", "alias"`
name(optional)| `string`| Represents the extra name.Example`"Johnny"`

### `NewImAddress`

iOS

Represents a new instant messaging address to be added to a contact.

Property| Type| Description
---|---|---
label(optional)| `string`| Label associated with the instant messaging address. If not provided, a label "other" will be used.Example`"WhatsApp"`
service(optional)| `string`| The name of the instant message address service.Example`"WhatsApp", "Skype"`
username(optional)| `string`| The user name for instant message service address.Example`"user123"`

### `NewPhone`

Android

iOS

Represents a new phone number to be added to a contact.

Property| Type| Description
---|---|---
label(optional)| `string`| Label associated with the phone number. If not provided, a label "other" will be used.Example`"work", "home"`
number(optional)| `string`| Represents the phone number. It is recommended to use E.164 format for phone numbers. The database does not enforce any specific format, so any string can be used.Example`"+12123456789"`

### `NewRelation`

Android

iOS

Represents a new relation (brother, sister, etc.) to be added to a contact.

Property| Type| Description
---|---|---
label(optional)| `string`| Label associated with the relation. If not provided, a label "other" will be used.Example`"brother", "sister"`
name(optional)| `string`| The name of the relative.Example`"Anna"`

### `NewSocialProfile`

Android

iOS

Represents a new social profile to be added to a contact.

Property| Type| Description
---|---|---
label(optional)| `string`| Label associated with the social profile.
service(optional)| `string`| Service name (e.g., Twitter, Facebook).
url(optional)| `string`| URL to the social profile.
userId(optional)| `string`| User identifier for the social profile.
username(optional)| `string`| Username for the social profile.

### `NewUrlAddress`

Android

iOS

Represents a new URL address to be added to a contact.

Property| Type| Description
---|---|---
label(optional)| `string`| Label associated with the URL address. If not provided, a label "other" will be used.Example`"homepage", "blog"`
url(optional)| `string`| The URL address.Example`"https://example.com"`

### `NonGregorianBirthday`

iOS

Represents a non-Gregorian birthday date.

Property| Type| Description
---|---|---
calendar| `NonGregorianCalendar`| The calendar system used for the date.
day| `number`| Day component of the date in format 1-31.
month| `number`| Month component of the date in format 1-12.
year(optional)| `number`| Year component of the date. For birthday dates, this field can be omitted to represent a date without a year.

### `PartialContactDetails`

Android

iOS

Type: extended by:

Property| Type| Description
---|---|---
id| `string`| -

## Enums

### `ContactField`

Android

iOS

Enum representing the various fields of a contact. These fields can be used to specify which details to retrieve from a contact.

#### `ADDRESSES`

`ContactField.ADDRESSES ＝ "addresses"`

#### `BIRTHDAY`

`ContactField.BIRTHDAY ＝ "birthday"`

#### `COMPANY`

`ContactField.COMPANY ＝ "company"`

#### `DATES`

`ContactField.DATES ＝ "dates"`

#### `DEPARTMENT`

`ContactField.DEPARTMENT ＝ "department"`

#### `EMAILS`

`ContactField.EMAILS ＝ "emails"`

#### `EXTRA_NAMES`

`ContactField.EXTRA_NAMES ＝ "extraNames"`

#### `FAMILY_NAME`

`ContactField.FAMILY_NAME ＝ "familyName"`

#### `FULL_NAME`

`ContactField.FULL_NAME ＝ "fullName"`

#### `GIVEN_NAME`

`ContactField.GIVEN_NAME ＝ "givenName"`

#### `IM_ADDRESSES`

`ContactField.IM_ADDRESSES ＝ "imAddresses"`

#### `IMAGE`

`ContactField.IMAGE ＝ "image"`

#### `IS_FAVOURITE`

`ContactField.IS_FAVOURITE ＝ "isFavourite"`

#### `JOB_TITLE`

`ContactField.JOB_TITLE ＝ "jobTitle"`

#### `MAIDEN_NAME`

`ContactField.MAIDEN_NAME ＝ "maidenName"`

#### `MIDDLE_NAME`

`ContactField.MIDDLE_NAME ＝ "middleName"`

#### `NICKNAME`

`ContactField.NICKNAME ＝ "nickname"`

#### `NON_GREGORIAN_BIRTHDAY`

`ContactField.NON_GREGORIAN_BIRTHDAY ＝ "nonGregorianBirthday"`

#### `NOTE`

`ContactField.NOTE ＝ "note"`

#### `PHONES`

`ContactField.PHONES ＝ "phones"`

#### `PHONETIC_COMPANY_NAME`

`ContactField.PHONETIC_COMPANY_NAME ＝ "phoneticCompanyName"`

#### `PHONETIC_FAMILY_NAME`

`ContactField.PHONETIC_FAMILY_NAME ＝ "phoneticFamilyName"`

#### `PHONETIC_GIVEN_NAME`

`ContactField.PHONETIC_GIVEN_NAME ＝ "phoneticGivenName"`

#### `PHONETIC_MIDDLE_NAME`

`ContactField.PHONETIC_MIDDLE_NAME ＝ "phoneticMiddleName"`

#### `PREFIX`

`ContactField.PREFIX ＝ "prefix"`

#### `RELATIONS`

`ContactField.RELATIONS ＝ "relations"`

#### `SOCIAL_PROFILES`

`ContactField.SOCIAL_PROFILES ＝ "socialProfiles"`

#### `SUFFIX`

`ContactField.SUFFIX ＝ "suffix"`

#### `THUMBNAIL`

`ContactField.THUMBNAIL ＝ "thumbnail"`

#### `URL_ADDRESSES`

`ContactField.URL_ADDRESSES ＝ "urlAddresses"`

### `ContactsSortOrder`

Android

iOS

Enum representing the sort order options for querying contacts.

#### `FamilyName`

`ContactsSortOrder.FamilyName ＝ "familyName"`

#### `GivenName`

`ContactsSortOrder.GivenName ＝ "givenName"`

#### `None`

`ContactsSortOrder.None ＝ "none"`

#### `UserDefault`

`ContactsSortOrder.UserDefault ＝ "userDefault"`

### `NonGregorianCalendar`

iOS

Enum representing non-Gregorian calendar types.

#### `buddhist`

`NonGregorianCalendar.buddhist ＝ "buddhist"`

#### `chinese`

`NonGregorianCalendar.chinese ＝ "chinese"`

#### `coptic`

`NonGregorianCalendar.coptic ＝ "coptic"`

#### `ethiopicAmeteAlem`

`NonGregorianCalendar.ethiopicAmeteAlem ＝ "ethiopicAmeteAlem"`

#### `ethiopicAmeteMihret`

`NonGregorianCalendar.ethiopicAmeteMihret ＝ "ethiopicAmeteMihret"`

#### `hebrew`

`NonGregorianCalendar.hebrew ＝ "hebrew"`

#### `indian`

`NonGregorianCalendar.indian ＝ "indian"`

#### `islamic`

`NonGregorianCalendar.islamic ＝ "islamic"`

#### `islamicCivil`

`NonGregorianCalendar.islamicCivil ＝ "islamicCivil"`

#### `japanese`

`NonGregorianCalendar.japanese ＝ "japanese"`

#### `persian`

`NonGregorianCalendar.persian ＝ "persian"`

#### `republicOfChina`

`NonGregorianCalendar.republicOfChina ＝ "republicOfChina"`

## Permissions

### Android

This library automatically adds `READ_CONTACTS` and `WRITE_CONTACTS` permissions to your app:

Android Permission| Description
---|---
`READ_CONTACTS`| Allows an application to read the user's contacts data.
`WRITE_CONTACTS`| Allows an application to write the user's contacts data.

### iOS

The following usage description keys are used by this library:

Info.plist Key| Description
---|---
`NSContactsUsageDescription`| A message that tells the user why the app is requesting access to the user’s contacts.