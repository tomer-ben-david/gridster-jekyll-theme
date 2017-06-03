---
layout: post
title:  "Design Patterns CheatSheet"
date:   2017-04-22 22:18:00
categories: cheatsheet,design-patterns,dev
comments: true
---
## Brief Summary of design patterns

## Creational

**Factory Method**: Rider --> RoadBikeCreator extends AbstractBikeCreator .create()

**Abstract Factory**: Rider(AbstractBikeCreator c).ride() { c.purchaseBike().ride()) ; // someone from the outside is passing the factory.
 
**Static Factory**: Rider.createBike(whichBike).ride(); // createBike is static.

**Singleton**: Object StringUtils {}

**Builder**: new BikeBuilder.setGear(.).setChain(.).build()

**Prototype**: Rider --> BikePrototype.clone(color="red") // Great I have a new red bike.

## Structural

**Adapter**: AppLogger.warn(msg) { this.logger.log(msg, WARN) }

**Decorator**: SecuredFileUploader(FileUploader).upload(checkSecured(file); FileUploader.upload())

**Bridge**: Encrypter(hasher: Hasher).encrypt() { hasher.hash() } we separated hasher implementation which can ba a whole implementation hierarchy from our encryption, by mere of a field pointing to a different class or class hierarchy).

**Composite**: Node.print <- Tree.print <- Leaf.print => foreach.print // (Tree/Node/Leaf group/aggregation treated as single)

**Facade**: AirplaneFacade(controls, handle, break).flight(control.start, handle.stop...) // Wrap complex system in a simple interface.

**Flyweight**: `Circle.get(color) = cache.getOrCreate(circle)` // Reduce memory used with objects that share data with similar objects

**Proxy**: `FileReaderProxy extends AbstractFileReader .read() { new RealFileReader.read() }` // like access control to object for example expensive resources access...

## Behavioral

**Value Object**: `val SUNDAY = new Integer(1)` // small immutable multithreaded

**null object**: `NullMessage,TextMessage extends AbstractMessage` // define actual object representing null

**Strategy**: `Parser.parse { filename.match('json') return JSONParser else return CSVParser }` // choose algorithm from family at runtime

**Command**: Client --> Invoker contains Concrete Commands --> Executes..

**Chain Of Responsibility**: Client --> ConcreteHandler1.handleRequest() --> ConcreteHandler2... 

**Interpreter**: Interprester.interepet { operator.match { case "+": new Add } } // Represent a domain with a language.

**Iterator**: StudentIterator extends Iterator { override hasNext(); override next() }
 
**Mediator**: GoogleGroupsMembersMedaitor.sendMessage // (instead of external class keeping track of who is in members)

**Memento**: TextEditor.undo() { memento.getPrevState() } // undo - rollback / restore object state

**Observer**

**State**: `PausedState.press() { setState(PlayState); }; PlayState.pressed() { setState(PausedState); }` // like strategy design pattern state is about how algorithm performaed state is about what action is performed.

**Template Method**: `DataFinderExample.find { jsonDatafinder extends DataFinder findInJson, csvDataFinder find in csv }` // common skeleton for algorithm different implementations deal with specifics defer algorithm impl to subsclasses.

**Visitor**: `Text.accept(htmlExporterVisitor) { htmlExporterVisitor.visit(this); }; htmlExporterVisitor.print(); } Not all use cases are known at design of class.
