---
ms.openlocfilehash: e56caa7b2fabb4b5ade242ec43f4592689e8ba3d
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876831"
---
<a name="c-language-specification"></a>Specifiche del linguaggio C#
===========================

__Versione 6__

Si tratta di una bozza non ufficiale, pubblicata qui per praticità. Quando si invia una C# proposta di specifica 6,0 ad ECMA, questa verrà condivisa qui.

<!--
(This document is also available for download: [csharp.pdf](CSharp%20Language%20Specification.pdf?raw=true) and [csharp.docx](CSharp%20Language%20Specification.docx?raw=true))
-->

* [Introduzione](introduction.md)
    * [Hello world](introduction.md#hello-world)
    * [Struttura del programma](introduction.md#program-structure)
    * [Tipi e variabili](introduction.md#types-and-variables)
    * [Espressioni](introduction.md#expressions)
    * [Istruzioni](introduction.md#statements)
    * [Classi e oggetti](introduction.md#classes-and-objects)
* [Struttura lessicale](lexical-structure.md)
    * [Programmi](lexical-structure.md#programs)
    * [Grammatiche](lexical-structure.md#grammars)
    * [Analisi lessicale](lexical-structure.md#lexical-analysis)
    * [Token](lexical-structure.md#tokens)
    * [Direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives)
* [Concetti di base](basic-concepts.md)
    * [Avvio dell'applicazione](basic-concepts.md#application-startup)
    * [Chiusura dell'applicazione](basic-concepts.md#application-termination)
    * [Dichiarazioni](basic-concepts.md#declarations)
    * [Membri](basic-concepts.md#members)
    * [Accesso ai membri](basic-concepts.md#member-access)
    * [Firme e overload](basic-concepts.md#signatures-and-overloading)
    * [Ambiti](basic-concepts.md#scopes)
    * [Nomi di tipi e spazi dei nomi](basic-concepts.md#namespace-and-type-names)
    * [Automatic Memory Management](basic-concepts.md#automatic-memory-management)
    * [Ordine di esecuzione](basic-concepts.md#execution-order)
* [Tipi](types.md)
    * [Tipi valore](types.md#value-types)
    * [Tipi riferimento](types.md#reference-types)
    * [Boxing e unboxing](types.md#boxing-and-unboxing)
    * [Tipi costruiti](types.md#constructed-types)
    * [Parametri di tipo](types.md#type-parameters)
    * [Tipi di struttura ad albero delle espressioni](types.md#expression-tree-types)
    * [Tipo dinamico](types.md#the-dynamic-type)
* [Variabili](variables.md)
    * [Categorie di variabili](variables.md#variable-categories)
    * [Valori predefiniti](variables.md#default-values)
    * [Assegnazione definita](variables.md#definite-assignment)
    * [Riferimenti alle variabili](variables.md#variable-references)
    * [Atomicità dei riferimenti alle variabili](variables.md#atomicity-of-variable-references)
* [Conversioni](conversions.md)
    * [Conversioni implicite](conversions.md#implicit-conversions)
    * [Conversioni esplicite](conversions.md#explicit-conversions)
    * [Conversioni standard](conversions.md#standard-conversions)
    * [Conversioni definite dall'utente](conversions.md#user-defined-conversions)
    * [Conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)
    * [Conversioni dei gruppi di metodi](conversions.md#method-group-conversions)
* [Espressioni](expressions.md)
    * [Classificazioni delle espressioni](expressions.md#expression-classification)
    * [Binding statico e dinamico](expressions.md#static-and-dynamic-binding)
    * [Operatori](expressions.md#operators)
    * [Ricerca di membri](expressions.md#member-lookup)
    * [Membri di funzione](expressions.md#function-members)
    * [Espressioni primarie](expressions.md#primary-expressions)
    * [Operatori unari](expressions.md#unary-operators)
    * [Operatori aritmetici](expressions.md#arithmetic-operators)
    * [Operatori shift](expressions.md#shift-operators)
    * [Operatori relazionali e di test del tipo](expressions.md#relational-and-type-testing-operators)
    * [Operatori logici](expressions.md#logical-operators)
    * [Operatori logici condizionali](expressions.md#conditional-logical-operators)
    * [Operatore null-coalescing](expressions.md#the-null-coalescing-operator)
    * [Operatore condizionale](expressions.md#conditional-operator)
    * [Espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)
    * [Espressioni di query](expressions.md#query-expressions)
    * [Operatori di assegnazione](expressions.md#assignment-operators)
    * [Espressione](expressions.md#expression)
    * [Espressioni costanti](expressions.md#constant-expressions)
    * [Espressioni booleane](expressions.md#boolean-expressions)
* [Istruzioni](statements.md)
    * [Endpoint e raggiungibilità](statements.md#end-points-and-reachability)
    * [Blocchi](statements.md#blocks)
    * [Istruzione vuota](statements.md#the-empty-statement)
    * [Istruzioni con etichetta](statements.md#labeled-statements)
    * [Istruzioni di dichiarazione](statements.md#declaration-statements)
    * [Istruzioni di espressione](statements.md#expression-statements)
    * [Istruzioni di selezione](statements.md#selection-statements)
    * [Istruzioni di iterazione](statements.md#iteration-statements)
    * [Istruzioni di salto](statements.md#jump-statements)
    * [Istruzione Try](statements.md#the-try-statement)
    * [Istruzioni Checked e Unchecked](statements.md#the-checked-and-unchecked-statements)
    * [Istruzione Lock](statements.md#the-lock-statement)
    * [Istruzione Using](statements.md#the-using-statement)
    * [Istruzione Yield](statements.md#the-yield-statement)
* [Spazi dei nomi](namespaces.md)
    * [Unità di compilazione](namespaces.md#compilation-units)
    * [Dichiarazioni dello spazio dei nomi](namespaces.md#namespace-declarations)
    * [Alias extern](namespaces.md#extern-aliases)
    * [Direttive using](namespaces.md#using-directives)
    * [Membri dello spazio dei nomi](namespaces.md#namespace-members)
    * [Dichiarazioni di tipi](namespaces.md#type-declarations)
    * [Qualificatori di alias dello spazio dei nomi](namespaces.md#namespace-alias-qualifiers)
* [Classi](classes.md)
    * [Dichiarazioni di classe](classes.md#class-declarations)
    * [Tipi parziali](classes.md#partial-types)
    * [Membri di classi](classes.md#class-members)
    * [Costanti](classes.md#constants)
    * [Fields](classes.md#fields)
    * [Metodi](classes.md#methods)
    * [Proprietà](classes.md#properties)
    * [Eventi](classes.md#events)
    * [Indicizzatori](classes.md#indexers)
    * [Operatori](classes.md#operators)
    * [Costruttori di istanza](classes.md#instance-constructors)
    * [Costruttori statici](classes.md#static-constructors)
    * [Distruttori](classes.md#destructors)
    * [Iteratori](classes.md#iterators)
    * [Funzioni asincrone](classes.md#async-functions)
* [Struct](structs.md)
    * [Dichiarazioni struct](structs.md#struct-declarations)
    * [Membri struct](structs.md#struct-members)
    * [Differenze di classi e struct](structs.md#class-and-struct-differences)
    * [Esempi di struct](structs.md#struct-examples)
* [Matrici](arrays.md)
    * [Tipi di matrice](arrays.md#array-types)
    * [Creazione di matrici](arrays.md#array-creation)
    * [Accesso agli elementi di matrice](arrays.md#array-element-access)
    * [Membri di matrice](arrays.md#array-members)
    * [Covarianza di matrici](arrays.md#array-covariance)
    * [Inizializzatori di matrice](arrays.md#array-initializers)
* [Interfacce](interfaces.md)
    * [Dichiarazioni di interfaccia](interfaces.md#interface-declarations)
    * [Membri di interfaccia](interfaces.md#interface-members)
    * [Nomi completi dei membri di interfaccia](interfaces.md#fully-qualified-interface-member-names)
    * [Implementazioni di interfacce](interfaces.md#interface-implementations)
* [Enumerazioni](enums.md)
    * [Dichiarazioni enum](enums.md#enum-declarations)
    * [Modificatori enum](enums.md#enum-modifiers)
    * [Membri enum](enums.md#enum-members)
    * [Il tipo System.Enum](enums.md#the-systemenum-type)
    * [Valori e operazioni di enumerazione](enums.md#enum-values-and-operations)
* [Delegati](delegates.md)
    * [Dichiarazioni Delegate](delegates.md#delegate-declarations)
    * [Compatibilità dei delegati](delegates.md#delegate-compatibility)
    * [Creazione di un'istanza del delegato](delegates.md#delegate-instantiation)
    * [Chiamata a un delegato](delegates.md#delegate-invocation)
* [Eccezioni](exceptions.md)
    * [Cause delle eccezioni](exceptions.md#causes-of-exceptions)
    * [La classe System.Exception](exceptions.md#the-systemexception-class)
    * [Gestione delle eccezioni](exceptions.md#how-exceptions-are-handled)
    * [Classi di eccezioni comuni](exceptions.md#common-exception-classes)
* [Attributi](attributes.md)
    * [Classi di attributi](attributes.md#attribute-classes)
    * [Specifica di attributo](attributes.md#attribute-specification)
    * [Istanze di attributi](attributes.md#attribute-instances)
    * [Attributi riservati](attributes.md#reserved-attributes)
    * [Attributi per l'interoperabilità](attributes.md#attributes-for-interoperation)
* [Codice di tipo unsafe](unsafe-code.md)
    * [Contesti non sicuri](unsafe-code.md#unsafe-contexts)
    * [Tipi di puntatori](unsafe-code.md#pointer-types)
    * [Variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)
    * [Conversioni puntatore](unsafe-code.md#pointer-conversions)
    * [Puntatori nelle espressioni](unsafe-code.md#pointers-in-expressions)
    * [Istruzione fixed](unsafe-code.md#the-fixed-statement)
    * [Buffer a dimensione fissa](unsafe-code.md#fixed-size-buffers)
    * [Allocazione nello stack](unsafe-code.md#stack-allocation)
    * [Allocazione di memoria dinamica](unsafe-code.md#dynamic-memory-allocation)
* [Commenti sulla documentazione](documentation-comments.md)
    * [Introduzione](documentation-comments.md#introduction)
    * [Tag consigliati](documentation-comments.md#recommended-tags)
    * [Elaborazione del file di documentazione](documentation-comments.md#processing-the-documentation-file)
    * [Un esempio](documentation-comments.md#an-example)

<!--
* Grammar: [csharp.html](http://ljw1004.github.io/csharpspec/csharp.html). Or download in ANTLR format: [csharp.g4](csharp.g4?raw=true). 
-->
