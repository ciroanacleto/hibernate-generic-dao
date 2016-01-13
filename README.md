# hibernate-generic-dao
Automatically exported from code.google.com/p/hibernate-generic-dao

## Hibernate Generic D.A.O. Framework

### []()The motivation behind the framework

We had worked on a project where we hand-coded all of our DAOs. This produced four irksome difficulties: (1) Method names and implementations were not altogether consistent. (2) It was a pain to make additional columns sortable or filterable, and as a result, a lot of pages lacked good sorting and filtering. (3) Making additional DAOs was tedious and took a fair amount of time. (4) Writing tests for DAOs is tricky and tedious, so no one really did.

This framework aims to ease our troubles.

### []()Why might you consider looking into this framework?

- **_Generic DAO:_** With the sweetness of Java generics, the concept of generic DAOs is not new, and it’s not difficult. However, we’ve put a lot of work into making these easy to use and robust. So if you like the way we’ve done it, then this framework provides **ready-made code** for you. On the other hand if you’d rather make your own, then simply feel free to **look at our source code for ideas** and help on a few potentially tricky issues.

- **_Search:_** Search is the most original and sophisticated part of this framework, and it **can be used with or without the generic DAO portion**of the framework. The search capability is designed around the use-case of a list page with **sorting, filtering, column selection and paging**. However, its use is certainly not limited to that. The value that the search adds is **simpler, more robust querying with less coding and less testing**. It is similar to Hibernate Criteria, but it is simpler to use and can easily move across layers of an application including view and even remote layers. Plus is works with both Hibernate and JPA

- **_Remote DAO (for R.I.A.s?):_** If you you’re like us, you don’t want to write and configure an individual DAO style remote service for each entity in a R.I.A. or other client-heavy application. This framework may have the solution. We provide utilities and sample code to **adapt our single general DAO to any remoting technology interface**. Just configure this single remote access point and the client can do any basic CRUD or search operation on any entity. Again, if you don’t like our way of doing things, maybe you can at least get some ideas from our source code.

- **_Remote Search (for R.I.A.s?):_** As mentioned above, the framework can provide a single point for client-server CRUD and search operations. The framework’s search is meant to be able to **cross the client-server boundary**. So lists and searches in the **client application can take advantage of the same easy-to-use features and consistency that the search functionality provides** in the server tier or single tier application. This consistency allowed us to create a reusable collection type in Adobe Flex 3 that is associated with a single search object and automatically updates itself from the server according to the search parameters.

*A fairly simple adapter is required for each JPA provider. Right now we only have an adapter for Hibernate Entity Manager. If anyone would like to contribute an adapter for any other JPA provider (OpenJPA, TopLink, etc.), that would be great.

**If time permits, we would like to eventually post our corresponding Adobe Flex 3 framework and utilities.

### []()More Information

Wiki Documentation: [UserGuide](https://code.google.com/p/hibernate-generic-dao/wiki/UserGuide)

Javadoc: [http://hibernate-generic-dao.googlecode.com/svn/trunk/docs/api/index.html](http://hibernate-generic-dao.googlecode.com/svn/trunk/docs/api/index.html)

Blog: [http://hibernategenericdao.wordpress.com/](http://hibernategenericdao.wordpress.com/)

### []()Questions and Comments

Please post at [http://groups.google.com/group/java-generic-dao](http://groups.google.com/group/java-generic-dao).

### []()Code Examples

#### []()Creating DAOs for individual model classes:

Simply extend the GenericDAO class with the specific type.

publicinterfaceProjectDAOextendsGenericDAO<Project,Long>{}publicclassProjectDAOImplextendsGenericDAOImpl<Project,Long>implementsProjectDAO{}

#### []()The following methods (and several more) are now available on ProjectDAO

```java
Project project = projectDAO.find(projectId);

List<Project> list = projectDAO.findAll();  
projectDAO.save(project);  
projectDAO.remove(project);  
  
projectDAO.removeById(project.getId());
Search search = newSearch();  
search.addFilterEqual("name","hibernate-generic-dao");
List<Project> list = projectDAO.search(search);
int count = projectDAO.count(search);
SearchResult<Project> result = projectDAO.searchAndCount(search);  
list = result.getResult();  
count = result.getTotalCount();  
  
search.clear();  
search.addField("rating",Field.OP_AVG);
int avgProjectRating =(Integer) prjoectDAO.searchUnique(search);
```
#### []()A GeneralDAO is also provided with DAO methods for any entity:

```java
publicinterfaceGeneralDAO{  
  
public<T> T find(Class<T> type,Serializable id);  
  
        public<T> T[] find(Class<T> type,Serializable... ids);  
  
        public<T> T getReference(Class<T> type,Serializable id);  
  
        public<T> T[] getReferences(Class<T> type,Serializable... ids);  
  
        publicboolean save(Object entity);  
  
        publicboolean[] save(Object... entities);  
  
        publicboolean remove(Object entity);  
  
        publicvoid remove(Object... entities);  
  
        publicboolean removeById(Class<?> type,Serializable id);  
  
        publicvoid removeByIds(Class<?> type,Serializable... ids);  
  
        public<T>List<T> findAll(Class<T> type);  
  
        publicList search(ISearch search);  
  
        publicObject searchUnique(ISearch search);  
  
        publicint count(ISearch search);  
  
        publicSearchResult searchAndCount(ISearch search);  
  
        publicboolean isAttached(Object entity);  
  
        publicvoid refresh(Object... entities);  
  
        publicvoid flush();  
  
        publicFilter getFilterFromExample(Object example);  
  
        publicFilter getFilterFromExample(Object example,ExampleOptions options);
}

```

#### []()Search DTO usage examples
```java
Search search =newSearch(Project.class); //filtering  
search.addFilterEqual("name","hibernate-generic-dao");  
  
search.addFilterLessThan("completionDate",newDate());  
  
search.addFilterOr(  
    Filter.equal("name","Jack"),  
    Filter.and(  
        Filter.equal("name","Jill"),  
        Filter.like("location","%Chicago%"),  
        Filter.greaterThan("age",5)  
    ));  
  
search.addFilterIn("name","Jack","Jill","Bob");  
  
search.addFilterNot(Filter.in("name","Jack","Jill","Bob")); //sorting  
search.addSort("name");  
search.addSort("age",true); //descending//projection  
search.addField("name");  
search.addField("location"); //or with column operators  
search.addField("rating",Field.OP_AVG);  
search.addField("developerCount",Field.OP_MAX);//paging  
search.setMaxResults(15); //a.k.a. results per page  
search.setPage(3); //controlling eager fetching of relationships  
serach.addFetch("owner");

```

#### []()Nested properties are also fully supported...[](https://code.google.com/p/hibernate-generic-dao/#Nested_properties_are_also_fully_supported...)

```java
search.addFilterEqual("status.name","active");  
search.addFilterGreaterThan("workgroup.manager.salary",75000.00);  
  
search.addSort("status.name");
```
