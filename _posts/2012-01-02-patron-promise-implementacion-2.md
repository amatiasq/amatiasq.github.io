---
layout: post
title: 'Patrón Promise: Implementación (II)'
draft: true
---

Continuando con la implementación del patrón Promise, vamos a continuar con la implementación del [post anterior][1]. He limpiado un poco la implementación que tenemos actualmente: [code lang="js"] function Promise() { this.\_callbacks = []; this.\_onError = []; this.\_state = 'waiting'; } Promise.prototype.then = function(callback, onError) { // Validamos el callback normal if (typeof callback !== 'function') throw new Error("[Promise.then] El argumento 'callback' no es una función " + typeof callback); // Validamos el callback de error. Como es opcional puede ser 'undefined' o una función if (onError && typeof onError !== 'function') throw new Error("[Promise.then] El argumento 'onError' no es una función " + typeof onError); this.\_callbacks.push(callback); // Si no era undefined debe ser una función, porque ya lo validamos if (onError) this.\_onError.push(onError); }; Promise.prototype.done = function(error) { if (this.\_state !== 'waiting') throw new Error('Intentando cumplir un promise que ya ha finalizado'); this.\_state = 'done'; // Guardamos los argumentos que se le ha pasado a .done() var args = arguments; var callback; for (var i = 0; i < this.\_callbacks.length; i++) { callback = this.\_callbacks[i]; // Y se los pasamos al callback callback.apply(null, args); } }; Promise.prototype.fail = function(error) { if (this.\_state !== 'waiting') throw new Error('Intentando hacer fallar un promise que ya ha finalizado'); this.\_state = 'failed'; var callback; for (var i = 0; i < this.\_onError.length; i++) { callback = this._onError[i]; callback(error); } }; [/code] Empezaremos por el método .and()

 [1]: 2011/12/18/patron-promise-implementacion-i/
