This is a Vaadin 7 add-on component.

Maven Dependency

```xml
<dependency>
   <groupId>org.vaadin.addons</groupId>
   <artifactId>pagedtable</artifactId>
   <version>0.6.7</version>
</dependency>

<repository>
   <id>qiiip-repo</id>
   <url>http://qiiip.org/mavenRepo</url>
</repository>
```

Simple Example (in Java)

```java
public class PagedtableExample extends UI {

    @Override
    protected void init(final VaadinRequest request) {
        PagedTable table = new PagedTable("PagedTable Example");
        ControlsLayout controls = table.createControls();

        BeanItemContainer container = new BeanItemContainer(User.class);
        table.setContainerDataSource(container);

        layout.addComponent(table);
        layout.addComponent(controls);
        setContent(layout);
    }
}
```

Complex Example with Lazy Loading (in Java)

```java
public class PagedtableExample extends UI {

    @Override
    protected void init(final VaadinRequest request) {
        VerticalLayout mainLayout = new VerticalLayout();
        VerticalLayout tableLayout = new VerticalLayout();
        tableLayout.setSizeUndefined();

        PagedTable table = createLazyTable();
        ControlsLayout controlsLayout = table.createControls();

        controlsLayout.getItemsPerPageLabel().setValue("Other label...");

        tableLayout.addComponent(table);
        tableLayout.addComponent(controlsLayout);
        mainLayout.addComponent(tableLayout);
        mainLayout.setComponentAlignment(tableLayout, Alignment.MIDDLE_CENTER);
        mainLayout.setMargin(true);
        VerticalLayout layout = new VerticalLayout();
        layout.addComponent(mainLayout);
        setContent(layout);
    }

    public PagedTable createLazyTable() {
        PagedTable pagedTable = new PagedTable("PagedTable example.");
        LazyLoadedContainerExamle container = new LazyLoadedContainerExamle(UserExample.class);
        pagedTable.setContainerDataSource(container);
        pagedTable.setWidth("1000px");
        pagedTable.setPageLength(10);
        pagedTable.setImmediate(true);
        pagedTable.setSelectable(true);
        pagedTable.setAlwaysRecalculateColumnWidths(true);
        pagedTable.addGeneratedColumn("Generated", new ColumnGenerator() {

            private static final long serialVersionUID = -5042109683675242407L;

            public Component generateCell(Table source, Object itemId,
                                          Object columnId) {
                Item item = source.getItem(itemId);
                return new Label("generated column");
            }
        });
        Object[] visibleColumns = {"name" , "Generated"};
        pagedTable.setVisibleColumns(visibleColumns);
        return pagedTable;
    }
}
public class UserExample {

    private String name;

    public UserExample(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
class LazyLoadedContainerExamle extends BeanContainer {

    private List<UserExample> dbFake = new ArrayList<UserExample>();

    {
        for (int i = 0; i < 1000; i++) {
            dbFake.add(new UserExample("Sara " + i));
            dbFake.add(new UserExample("Nicolas " + i));
            dbFake.add(new UserExample("Matthew " + i));
            dbFake.add(new UserExample("Michaela " + i));
            dbFake.add(new UserExample("Martin " + i));
            dbFake.add(new UserExample("Anna " + i));
        }
    }

    public LazyLoadedContainerExamle(Class type) {
        super(type);
    }

    @Override
    public int size() {
        // TODO: here you should get COUNT from database
        int size = dbFake.size();
        return size;
    }

    @Override
    public BeanItem getItem(Object itemId) {
        return new BeanItem((UserExample) itemId);
    }

    @Override
    public List<?> getItemIds(final int startIndex, final int numberOfItems) {
        int end = startIndex + numberOfItems;
        // TODO: here you should place fetching data from database (it should be paged SQL of course)
        System.out.println("start: " + startIndex + ", end: " + end);
        List<UserExample> res = dbFake.subList(startIndex, end);
        return res;
    }
}
```

Grails Dependency

```
mavenRepo "http://qiiip.org/mavenRepo"
compile "org.vaadin.addons:pagedtable:0.6.7"
```