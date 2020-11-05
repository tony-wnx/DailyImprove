## Spring @JsonSerialize自定义序列化过程解析

> Http请求是通过**RequestResponseBodyMethodProcessor**类来处理的

#### 步骤一

```java
public class RequestResponseBodyMethodProcessor extends AbstractMessageConverterMethodProcessor {
        ... ...
        ... ...
        // 返回请求时会调用该方法
        public void handleReturnValue(Object returnValue, MethodParameter returnType,
                                  ModelAndViewContainer mavContainer, NativeWebRequest webRequest)
            throws IOException, HttpMediaTypeNotAcceptableException, HttpMessageNotWritableException {

            mavContainer.setRequestHandled(true);
            ServletServerHttpRequest inputMessage = createInputMessage(webRequest);
            ServletServerHttpResponse outputMessage = createOutputMessage(webRequest);

            // Try even with null return value. ResponseBodyAdvice could get involved.
            writeWithMessageConverters(returnValue, returnType, inputMessage, outputMessage);
        }
}
```

#### 步骤二

```java
public abstract class AbstractMessageConverterMethodProcessor extends AbstractMessageConverterMethodArgumentResolver
        implements HandlerMethodReturnValueHandler {
        ... ...
        ... ...
	    protected <T> void writeWithMessageConverters(T value, MethodParameter returnType,
	        ServletServerHttpRequest inputMessage, ServletServerHttpResponse outputMessage)
	        throws IOException, HttpMediaTypeNotAcceptableException, HttpMessageNotWritableException {
            ... ...
            ... ...
            if (selectedMediaType != null) {
			selectedMediaType = selectedMediaType.removeQualityValue();
			for (HttpMessageConverter<?> messageConverter : this.messageConverters) {
				if (messageConverter instanceof GenericHttpMessageConverter) {
                    
                    // canwrite轮询判断是否可以序列化
					if (((GenericHttpMessageConverter) messageConverter).canWrite(
							declaredType, valueType, selectedMediaType)) {
						outputValue = (T) getAdvice().beforeBodyWrite(outputValue, returnType, selectedMediaType,
								(Class<? extends HttpMessageConverter<?>>) messageConverter.getClass(),
								inputMessage, outputMessage);
						if (outputValue != null) {
							addContentDispositionHeader(inputMessage, outputMessage);
                            
                            // 调用messageConverter.write()方法
							((GenericHttpMessageConverter) messageConverter).write(
									outputValue, declaredType, selectedMediaType, outputMessage);
							if (logger.isDebugEnabled()) {
								logger.debug("Written [" + outputValue + "] as \"" + selectedMediaType +
										"\" using [" + messageConverter + "]");
							}
						}
						return;
					}
				}
				else if (messageConverter.canWrite(valueType, selectedMediaType)) {
					outputValue = (T) getAdvice().beforeBodyWrite(outputValue, returnType, selectedMediaType,
							(Class<? extends HttpMessageConverter<?>>) messageConverter.getClass(),
							inputMessage, outputMessage);
					if (outputValue != null) {
						addContentDispositionHeader(inputMessage, outputMessage);
						((HttpMessageConverter) messageConverter).write(outputValue, selectedMediaType, outputMessage);
						if (logger.isDebugEnabled()) {
							logger.debug("Written [" + outputValue + "] as \"" + selectedMediaType +
									"\" using [" + messageConverter + "]");
						}
					}
					return;
				}
			}
		}
    }
}
```

* 默认转换器类型（this.messageConverters）

![image-20201105181423520](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20201105181423520.png)

* 调用write方法

  ![image-20201105182223714](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20201105182223714.png)

#### 步骤三

```java
public abstract class AbstractGenericHttpMessageConverter<T> extends AbstractHttpMessageConverter<T>
		implements GenericHttpMessageConverter<T> {
    ... ...
    ... ...
	public final void write(final T t, final Type type, MediaType contentType, HttpOutputMessage outputMessage)
			throws IOException, HttpMessageNotWritableException {

		final HttpHeaders headers = outputMessage.getHeaders();
		addDefaultHeaders(headers, t, contentType);

		if (outputMessage instanceof StreamingHttpOutputMessage) {
			StreamingHttpOutputMessage streamingOutputMessage = (StreamingHttpOutputMessage) outputMessage;
			streamingOutputMessage.setBody(new StreamingHttpOutputMessage.Body() {
				@Override
				public void writeTo(final OutputStream outputStream) throws IOException {
                    
                    // 调用writeInternal()方法
					writeInternal(t, type, new HttpOutputMessage() {
						@Override
						public OutputStream getBody() throws IOException {
							return outputStream;
						}
						@Override
						public HttpHeaders getHeaders() {
							return headers;
						}
					});
				}
			});
		}
		else {
			writeInternal(t, type, outputMessage);
			outputMessage.getBody().flush();
		}
	}
    ... ...
    ... ...
    // 调用实现类AbstractJackson2HttpMessageConverter.writeInternal()方法
	protected abstract void writeInternal(T t, Type type, HttpOutputMessage outputMessage)
			throws IOException, HttpMessageNotWritableException;
}
```

* 调用writeInternal方法

  ![image-20201105182853871](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20201105182853871.png)

#### 步骤四

```java
public abstract class AbstractJackson2HttpMessageConverter extends AbstractGenericHttpMessageConverter<Object> {
    ... ...
    ... ...
    @Override
	protected void writeInternal(Object object, Type type, HttpOutputMessage outputMessage)
			throws IOException, HttpMessageNotWritableException {

		MediaType contentType = outputMessage.getHeaders().getContentType();
		JsonEncoding encoding = getJsonEncoding(contentType);
		JsonGenerator generator = this.objectMapper.getFactory().createGenerator(outputMessage.getBody(), encoding);
		try {
			writePrefix(generator, object);

			Class<?> serializationView = null;
			FilterProvider filters = null;
			Object value = object;
			... ...
            ... ...
            // 调用writeValue()方法
			objectWriter.writeValue(generator, value);

			writeSuffix(generator, object);
			generator.flush();

		}
		catch (JsonProcessingException ex) {
			throw new HttpMessageNotWritableException("Could not write JSON: " + ex.getOriginalMessage(), ex);
		}
	}
}
```

* value：待序列化对象

  ![image-20201105183501577](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20201105183501577.png)

#### 步骤五

```java
public class ObjectWriter implements Versioned,java.io.Serializable // since 2.1{
	... ...
    ... ...
    public void writeValue(JsonGenerator gen, Object value) throws IOException{
        _configureGenerator(gen);
        if (_config.isEnabled(SerializationFeature.CLOSE_CLOSEABLE)
                && (value instanceof Closeable)) {

            Closeable toClose = (Closeable) value;
            try {
                
                // 调用serialize()方法
                _prefetch.serialize(gen, value, _serializerProvider());
                if (_config.isEnabled(SerializationFeature.FLUSH_AFTER_WRITE_VALUE)) {
                    gen.flush();
                }
            } catch (Exception e) {
                ClassUtil.closeOnFailAndThrowAsIAE(null, toClose, e);
                return;
            }
            toClose.close();
        } else {
            
            // 调用serialize()方法
            _prefetch.serialize(gen, value, _serializerProvider());
            if (_config.isEnabled(SerializationFeature.FLUSH_AFTER_WRITE_VALUE)) {
                gen.flush();
            }
        }
    }
	... ...
    ... ...
    public final static class Prefetch implements java.io.Serializable{
	    ... ...
        ... ...
        public void serialize(JsonGenerator gen, Object value, DefaultSerializerProvider prov)
            throws IOException{
            if (typeSerializer != null) {
                prov.serializePolymorphic(gen, value, rootType, valueSerializer, typeSerializer);
            } else  if (valueSerializer != null) {
                prov.serializeValue(gen, value, rootType, valueSerializer);
            } else if (rootType != null) {
                prov.serializeValue(gen, value, rootType);
            } else {
                
                // 调用DefaultSerializerProvider.serializeValue()方法
                prov.serializeValue(gen, value);
            }
        }
    }
}
```

#### 步骤六

```java
public abstract class DefaultSerializerProvider
    extends SerializerProvider
    implements java.io.Serializable // since 2.1; only because ObjectWriter needs it{
    ... ...
    ... ...
    public void serializeValue(JsonGenerator gen, Object value) throws IOException{
        _generator = gen;
        if (value == null) {
            _serializeNull(gen);
            return;
        }
        Class<?> cls = value.getClass();
        // true, since we do want to cache root-level typed serializers (ditto for null property)
        final JsonSerializer<Object> ser = findTypedValueSerializer(cls, true, null);

        // Ok: should we wrap result in an additional property ("root name")?
        final boolean wrap;
        PropertyName rootName = _config.getFullRootName();

        if (rootName == null) { // not explicitly specified
            wrap = _config.isEnabled(SerializationFeature.WRAP_ROOT_VALUE);
            if (wrap) {
                gen.writeStartObject();
                PropertyName pname = _config.findRootName(value.getClass());
                gen.writeFieldName(pname.simpleAsEncoded(_config));
            }
        } else if (rootName.isEmpty()) {
            wrap = false;
        } else { // [JACKSON-764]
            // empty String means explicitly disabled; non-empty that it is enabled
            wrap = true;
            gen.writeStartObject();
            gen.writeFieldName(rootName.getSimpleName());
        }
        try {
            
            // 调用serialize()方法
            ser.serialize(value, gen, this);
            if (wrap) {
                gen.writeEndObject();
            }
        } catch (IOException ioe) { // As per [JACKSON-99], pass IOException and subtypes as-is
            throw ioe;
        } catch (Exception e) { // but wrap RuntimeExceptions, to get path information
            String msg = e.getMessage();
            if (msg == null) {
                msg = "[no message for "+e.getClass().getName()+"]";
            }
            throw new JsonMappingException(gen, msg, e);
        }
    }
}
```

* 实现类

  ![image-20201105184633189](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20201105184633189.png)

#### 步骤七

```java
public class BeanSerializer extends BeanSerializerBase{
	... ...
    ... ...
    @Override
    public final void serialize(Object bean, JsonGenerator gen, SerializerProvider provider)
        throws IOException
    {
        if (_objectIdWriter != null) {
            gen.setCurrentValue(bean); // [databind#631]
            _serializeWithObjectId(bean, gen, provider, true);
            return;
        }
        gen.writeStartObject(bean);
        if (_propertyFilterId != null) {
            serializeFieldsFiltered(bean, gen, provider);
        } else {
            
            // 
            serializeFields(bean, gen, provider);
        }
        gen.writeEndObject();
    }
	... ...
    ... ...
}
```

#### 步骤八

```java
public abstract class BeanSerializerBase
    extends StdSerializer<Object>
    implements ContextualSerializer, ResolvableSerializer,
        JsonFormatVisitable, SchemaAware{
	... ...
    ... ...
    protected void serializeFields(Object bean, JsonGenerator gen, SerializerProvider provider)
        throws IOException
    {
        final BeanPropertyWriter[] props;
	    ... ...
        ... ...
        try {
            for (final int len = props.length; i < len; ++i) {
                BeanPropertyWriter prop = props[i];
                if (prop != null) { // can have nulls in filtered list
                    
                    // 根据各个属性的序列化方式，序列化各个字段
                    prop.serializeAsField(bean, gen, provider);
                }
            }
            if (_anyGetterWriter != null) {
                _anyGetterWriter.getAndSerialize(bean, gen, provider);
            }
        } 
        ... ...
        ... ...
    }
}
```

* prop属性

  * 实体类中的wxId字段上没有使用@JsonSerialize注解，接口返回时，使用默认的序列化方式

  ![image-20201105185312498](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20201105185312498.png)

  ​	

  * 实体类中nickname属性添加了@JsonSerialize(using = EmojiSerializer.class)注解，接口返回时，会调用EmojiSerializer类中的serialize()方法进行序列化

![image-20201105195428773](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20201105195428773.png)

#### 步骤九

```java
@JacksonStdImpl
public class BeanPropertyWriter extends PropertyWriter implements java.io.Serializable{
    ... ...
    ... ...
	@Override
    public void serializeAsField(Object bean, JsonGenerator gen,
            SerializerProvider prov) throws Exception {
        // inlined 'get()'
        final Object value = (_accessorMethod == null) ? _field.get(bean)
                : _accessorMethod.invoke(bean);

        // Null handling is bit different, check that first
        if (value == null) {
            if (_nullSerializer != null) {
                gen.writeFieldName(_name);
                _nullSerializer.serialize(null, gen, prov);
            }
            return;
        }
        // then find serializer to use
        JsonSerializer<Object> ser = _serializer;
        if (ser == null) {
            Class<?> cls = value.getClass();
            PropertySerializerMap m = _dynamicSerializers;
            ser = m.serializerFor(cls);
            if (ser == null) {
                ser = _findAndAddDynamic(m, cls, prov);
            }
        }
        // and then see if we must suppress certain values (default, empty)
        if (_suppressableValue != null) {
            if (MARKER_FOR_EMPTY == _suppressableValue) {
                if (ser.isEmpty(prov, value)) {
                    return;
                }
            } else if (_suppressableValue.equals(value)) {
                return;
            }
        }
        // For non-nulls: simple check for direct cycles
        if (value == bean) {
            // three choices: exception; handled by call; or pass-through
            if (_handleSelfReference(bean, gen, prov, ser)) {
                return;
            }
        }
        gen.writeFieldName(_name);
        if (_typeSerializer == null) {
            
            // 调用自定义序列化类的serialize()方法
            ser.serialize(value, gen, prov);
        } else {
            ser.serializeWithType(value, gen, prov, _typeSerializer);
        }
    }
    ... ...
    ... ...
}
```

* debug值

  ![image-20201105200219411](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20201105200219411.png)

* ser.serialize(value, gen, prov)调用自定义序列化类的方法

  ![image-20201105200524127](C:\Users\yangxiongqian\AppData\Roaming\Typora\typora-user-images\image-20201105200524127.png)

#### 序列化的实体类

```java
@Data
@Table(name = "tb_wx_user")
@EqualsAndHashCode(callSuper = true)
@Accessors(chain = true)
public class WxUserEntity extends SuperEntity {

    private static final long serialVersionUID = 1L;

	private String wxId;
	private String openid;
	private String unionid;
    
	// 用户昵称
	@JsonSerialize(using = EmojiSerializer.class)
	private String nickname;
    ... ...
    ... ...
}
```

### 序列化流程图

![Spring序列化流程](C:\Users\yangxiongqian\Desktop\Spring序列化流程.png)