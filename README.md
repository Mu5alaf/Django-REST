# Django-REST-framework

<img src="./images/drf-logo2.png" alt="Picture" width="auto" height="auto">

---

## Serializers

- Serializers allow complex data such as querysets and model instances to be converted to native Python datatypes that can then be easily rendered into JSON, XML or other content types. Serializers also provide deserialization, allowing parsed data to be converted back into complex types, after first validating the incoming data.

## How serializer Works

- Define a Serializer Class: Create a serializer class that specifies the fields you want to include and the type of data they represent.
- Serialization: Convert complex data (like model instances or querysets) to native Python data types.
- Rendering: Convert these native Python data types into JSON (or another format) to be sent to the client.
- Deserialization: Convert JSON data received from the client into native Python data types, then into complex data types (like model instances) after validation.

## ModelSerializer

The ModelSerializer class provides a shortcut that lets you automatically create a Serializer class with fields that correspond to the Model fields.

1- model.py

```` python
  class Product(models.Model):
      name = models.CharField(max_length=200)
      description = models.TextField()
      price = models.DecimalField(max_digits=10, decimal_places=2)
      stock = models.PositiveBigIntegerField()
      image = models.ImageField(upload_to='images/project',null=True,blank=True)
````

2- serializer.py

````python
class ProductSerializers(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = (
            'id',
            'name',
            'description',
            'price',
            'stock',
        )
    
    #validation
    def vaildate_price(self, value):
        if value <= 0 :
            raise serializers.ValidationError("Value can not be Zero ")
        return value
````

- The ModelSerializer class is the same as a regular Serializer class, except that:

    1. It will automatically generate a set of fields for you, based on the model.
    2. It will automatically generate validators for the serializer, such as unique_togethe validators.
    3. It includes simple default implementations of .create() and .update().

## How to set Field level Validation on serialises

- we have `price` field in our model and we need to make sure the price not less than zero
we can write a  custom field-level validation by adding .validate_<field_name> methods to our Serializer subclass. These are similar to the .clean_<field_name> methods on Django forms.

  ```` python

    def vaildate_price(self, value):
        if value <= 0 :
            raise serializers.ValidationError("Value can not be Zero ")
        return value

  ````

## Responses

- Unlike regular HttpResponse objects, you do not instantiate Response objects with rendered content. Instead you pass in unrendered data, which may consist of any Python primitives.
  
  ````python
    @api_view(['GET'])
    def product_list(request):
        product = Product.objects.all()
        serializers = ProductSerializers(product,many=True)
        return Response(serializers.data)
  ````

<img src="./images/respons.png" alt="Picture" width="auto" height="auto">

## Nested Serializers , SerializerMethodField , Serializer Relations
