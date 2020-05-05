<h1>SpringBoot整合Swagger</h1>



* 导入依赖

  ```xml
  <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger2</artifactId>
      <version>2.9.2</version>
  </dependency>
  <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger-ui</artifactId>
      <version>2.9.2</version>
  </dependency>
  ```

  

* 配置类

  ```java
  @Configuration
  @EnableSwagger2//开始swagger
  public class Swagger {
      @Bean
      public Docket createRestApi(){
          Docket docket = new Docket(DocumentationType.SWAGGER_2);
          docket.pathMapping("/")
                  .select()
                  .apis(RequestHandlerSelectors.basePackage("com.test.web"))//路径
                  .paths(PathSelectors.any())
                  .build();
          return docket;
      }
      @Bean
      public ApiInfo createApiInfo(){
          return new ApiInfoBuilder()
                  .title("SwaggerTile")
                  .description("SwaggerDescription")
                  .termsOfServiceUrl("TermsOfServieUrl")
                  .contact(new Contact("name","url","email"))
                  .version("Version")
                  .build();
      }
  }
  ```

  

* 使用注解

  参数是否必须填写在@ApiImplicitParam中并无实际作用，只有@RequestParam中写了非必须才有效

  ```java
  @Api(tags = "写在类上")
  @ApiOperation(value = "接口功能",notes = "写在方法上")
  @ApiImplicitParams(
      	{
              @ApiImplicitParam(name="参数名",value="参数作用",defaultValue="默认值",required=false,dataType="参数类名"),
               @ApiImplicitParam(name="参数名",value="参数作用",defaultValue="默认值",required=false)...
  		}
  	)
  
  @ApiModel  写在作为请求参数的实体类上,需要在参数声明APi指定 dataType = "类名"，而且controller参数使用@RequestBody
  @ApiModelProperty(value="用户id") 写在 写在作为请求参数的实体属性上
  ```

* 访问：xxx/swagger-ui.html