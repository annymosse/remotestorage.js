remoteStorage.js
================

This is a library for adding remoteStorage support to your client-side app. See http://unhosted-tutorial.galfert.5apps.com/ for example usage.

Version and license
=======
This is version 0.4.5 of the library, and you can use it under AGPL or MIT license - whatever floats your boat. Pull requests are very welcome (if you're not on github you can email them to michiel at unhosted.org).

Code example
=========

    require(['./js/remoteStorage'], function(remoteStorage) {
      var bearerToken = remoteStorage.receiveToken();
      if(!bearerToken) {//when you first hit the page
        remoteStorage.getStorageInfo('user@example.com', function(err, storageInfo) {
          localStorage.storageInfo=JSON.stringify(storageInfo);
          window.location = remoteStorage.createOAuthAddress(storageInfo, ['sandwiches'], window.location.href);
        });
      } else {//after you come back from the OAuth dance
        var client = remoteStorage.createClient(JSON.parse(localStorage.storageInfo), 'sandwiches', bearerToken);
        client.put('foo', 'bar', function(err) {
          client.get('foo', function(err, data) {
            alert(data);
          });
        });
      }
    });


Function reference
=======

remoteStorage.getStorageInfo(userAddress, callback)
-------

    userAddress: string of the form 'user@host'
    callback: function(err, storageInfo)
    -err: null, or a string describing what went wrong
    -storageInfo: an object describing some details of the user's remoteStorage

remoteStorage.createOAuthAddress(storageInfo, categories, redirectUri) {}
-------

    storageInfo: the object you got from the getStorageInfo call
    categories: an array of strings describing categories of data you will be accessing. See https://github.com/unhosted/website/wiki/categories for a list.
    @returns: string, the url you should go to for the OAuth dance

remoteStorage.receiveToken() {}
-------

    @returns: when coming back from the OAuth dance, a string containing the bearer token. Otherwise, null.

remoteStorage.createClient(storageInfo, category, bearerToken) {}
-------

    storageInfo: the object you got from the getStorageInfo call
    category: one of the strings from the array you passed to createOAuthAddress earlier
    @returns: a Client

Client.get(key, callback) {}
-------
    
    key: a string, identifying which element you want to retrieve
    callback: function(err, data)
    -err: null, or a string describing what went wrong
    -data: undefined, or a string, that is the current value of 'key' within 'category' on the user's remoteStorage

Client.put(key, value, callback) {}
-------

    key: a string, identifying which element you want to assign value to
    value: a string you want to assign to the element identified by key
    data: a string that, if successful, will be the new value of 'key' within 'category' on the user's remoteStorage
    callback: function(err)
    -err: null, or a string describing what went wrong

Client.delete(key, callback) {}
-------

    key: a string, identifying which element you want to reset to undefined
    callback: function(err)
    -err: null, or a string describing what went wrong

