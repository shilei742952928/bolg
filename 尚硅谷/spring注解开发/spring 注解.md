创建bean 
```
@configuration   告诉spring这是一个注册类  
@Bean    给容器中注册bean 类型为返回值的类型   id默认为方法名 可以在参数中指定id
@ComponentScan 可以指定扫描的包
             excluderFilters = {@Filter(type=FilterType.ANNOTATION),class=Controller.class} 按照注解类型排除扫描  
                                也可以按照正则  自定义  类型 排除   
             includeFilter  同上  代表 只包含    需要关闭全部扫描 useDefaultFilters=false
             可以多次注解定义不同的扫描规则  
@ComponentScans   值为@ComponentScan数组  用来定义多个扫描规则
@Filter  指定规则时  传入的类要实现   TypeFilter接口   
                                      接口中参数  MetadataReader  读取到当前正在扫描的类的信息
                                                  MetadataReaderFactory  可以获取到其他任何类的信息
@scope   控制注册的bean是单实例还是多实例    参数  singleton   默认  单实例  容器启动时已经注入到容器中
                                                   prototype    多实例   调用时注入
@lazy   懒加载   针对单实例bean  将单实例bean的注入到容器的时间从启动时注入转换为调用时注入

@conditional({condition})  按照条件注入bean     传入condition数组    每个condition实现Condition接口
                                            方法参数   ConditionContext  判断条件能使用的上下文
                                                       AnnotatedTypeMetadata   注释信息
@import({Color.class}) 
                    1.导入组件，id默认时组件的全类名
                    2.importSelector  返回需要导入的组件的全类名数组  实现 importSelector 接口
                                                   方法参数  AnnotationMetadata当前标注@import注解类的所有注解信息
                    3.importBeanDefinitionRegistrar   
                                                  方法参数  AnnotationMetadata
                                                            BeanDefinitionRegistry   注册类  调用registerBeanDefinition方法手工注册bean
使用工厂模式创建bean


//创建一个Spring定义的FactoryBean
public class ColorFactoryBean implements FactoryBean<Color> {

	//返回一个Color对象，这个对象会添加到容器中
	@Override
	public Color getObject() throws Exception {
		// TODO Auto-generated method stub
		System.out.println("ColorFactoryBean...getObject...");
		return new Color();
	}

	@Override
	public Class<?> getObjectType() {
		// TODO Auto-generated method stub
		return Color.class;
	}

	//是单例？
	//true：这个bean是单实例，在容器中保存一份
	//false：多实例，每次获取都会创建一个新的bean；
	@Override
	public boolean isSingleton() {
		// TODO Auto-generated method stub
		return false;
	}

}
    @Bean
	public ColorFactoryBean colorFactoryBean(){
		return new ColorFactoryBean();
	}
	
	使用Spring提供的 FactoryBean（工厂Bean）;
	 * 		1）、默认获取到的是工厂bean调用getObject创建的对象
	 * 		2）、要获取工厂Bean本身，我们需要给id前面加一个&
	 * 			&colorFactoryBean
	
```
bean声明周期

```
1.通过@Bean注解指定初始化和销毁方法
@Bean(init-method=“init”，destory-method="destory")
 2.通过让bean实现InitializingBean 实现bean 的初始化  通过实现disposableBean实现bena的销毁
 
 3.使用JSR250注解
    @PostConstruct  bean创建及赋值之后来执行初始化方法
    @PreDestroy   容器销毁bean之前通知
    
4 实现spring接口BeanPostProcessor
    postProcessBeforeInitialization  bean初始化之前调用
    postProcessAfterInitialization   bean初始化之后调用
```
属性赋值

```
@value 
1.基本数值
2.spel表达式  #{}
3.读取配置文件的值 ${}   使用注解@propertySource  导入配置文件注解  @propertySource（value={classpath：/111.properties}）
```
自动装配

```
1.@Autowired   required  属性 指定是否必须
2.@Qualifier     指定装配bean
3.@Primary  指定首选装配bean

4.@Resource（JSR250）

5.@Inject（JSR330） 
```




















