# Dynamic Filtering\(동적필터\)

동적으로 소스내에서 필터링을 할 수도 있다. 

우선 `mapping `하는 곳에서 필터링을 적용한다. 

```java
@GetMapping("/filtering")
public MappingJacksonValue retrieveSomeBean() {
	SomeBean someBean = new SomeBean("value1", "value2", "value3");
	
	SimpleBeanPropertyFilter filter = SimpleBeanPropertyFilter.filterOutAllExcept("field1","field2");
	
	FilterProvider filters = new SimpleFilterProvider().addFilter("SomeBeanFilter", filter);
	MappingJacksonValue mapping = new MappingJacksonValue(someBean);
	mapping.setFilters(filters);
	
	return mapping;
}
```

`addFilter`를 한 후 `SomeBeanFilter `하는 게 중요합니다. 

그리고 그 `SomeBeanFilter`를 SomeBean 클래스에서 `JsonFilter `어노테이션으로 지정가능하다. 

```java
@JsonFilter("SomeBeanFilter")
public class SomeBean {
	private String field1;
	private String field2;
```



