# haffar-docs
hello world!

# whatsapp numbers:

1- +962788855956

just in case : 

2- +962786923387

# let's start:

for use the api u need few things to understand first

1- the api key

some endpoints needs api_key to access to it

2- only allowed methods are POST, GET only

3- in this docs i'm gonna mention every end point + its details

4- in error case the returned data gonna be like:

{‘error’: Error Line}

5- database fields if u want u can check it :
```python
class Product(models.Model):
    date = models.DateField(null=True, blank=True, auto_now_add=True)
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    expire_date = models.DateField(null=True, blank=True)
    status = models.CharField(
        max_length=100, null=True, choices=PRODUCT_STATUS)
    category = models.ForeignKey(
        'Category', on_delete=models.CASCADE, null=True)
    subCategory = models.ForeignKey(
        'SubCategory', on_delete=models.CASCADE, null=True)
    fields = models.JSONField()

class Category(models.Model):
    name = models.CharField(max_length=500, null=True, blank=True)
    photo = models.CharField(max_length=500, null=True, blank=True)
    status = models.CharField(
        choices=CATEGORY_STATUS, null=True, blank=True, max_length=100, default='approved')
    fields = models.JSONField(null=True, default=get_default)

class SubCategory(models.Model):
    mainCategory = models.ForeignKey(
        Category, on_delete=models.CASCADE, null=True)
    name = models.CharField(max_length=1000, blank=True, null=True)
    photo = models.CharField(max_length=1000, blank=True, null=True)

class AD(models.Model):
    name = models.CharField(max_length=100, null=True, blank=True)
    status = models.CharField(
        choices=ADS_STATUS, null=True, blank=True, max_length=100)
    photo = models.CharField(max_length=500, null=True, blank=True)
    date = models.DateTimeField(default=datetime.now, blank=True)
    expire_date = models.DateField(null=True, blank=True)
    contact = models.CharField(max_length=100, null=True)


class Comment(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    description = models.CharField(max_length=500, null=True, blank=True)
    product = models.ForeignKey(Product, on_delete=models.CASCADE)


class About(models.Model):
    app_name = models.CharField(max_length=100, null=True)
    icon_link = models.CharField(max_length=1000, null=True)
    privacy_policy = models.CharField(max_length=100000, null=True)
    about_us = models.CharField(max_length=10000, null=True)
    contact_number = models.CharField(max_length=100, null=True)
    whatsup_number = models.CharField(max_length=100, null=True)
    agree_text = models.CharField(max_length=100000, null=True)
    payment_info_text = models.CharField(max_length=100000, null=True)
    payment_info_link = models.CharField(
        max_length=1000, null=True, blank=True)
    product_expire_days = models.IntegerField(null=True, blank=True)
```

its so easy to read
and u can check every table and its fields and data type
—--------------------------------

6- when add new product the fields that the user gonna fill

have to be dynamic!

you gonna see that in this docs


# 1- products endpoints:
-------------------------------
1- create new product:


	- method : POST
	
	- endpoint : /product/create/

	- data to post : 
	
	{
	
	- ‘user’: user token,
	
	- ‘category’ : category id, Int
	
	- ‘fields’ : product fields, Object
	
	- ‘subcategory’: subcategory id, Int
	
	- 'photos' : product photos, Object
	
	}

----------------------------------------
photos object have to be like this :
```
{
	'image 1 name' : image,
	'image 2 name' : image,
	# etc...
}
```
```
image name have to be :

1- unique name
2- the name have to contain the prodcut ID + the number of photo (start counting from 1 )
3- the name for the image it self doesnt matter, the important thing is the key in the dict
3- supported types are 1- PNG, 2-JPG

example:

'photos' : {
	'productId-1.png': screensho1.png,
	'productId-2.png': sfsdf.png,
	'productId-3.png': sdffds.png,
}
```


----------------------------------------
product fields have to match its category fields

u can get product category fields by using category fields api 

----------------------------------------
```
category/subcategory/fields data depends on user choices
```

returned data : 
```python
{

'data' : {

	'user':user id,
	'category':category id,
	'fields': 'product fields', # {
	  # for example :
	  # 'title': 'product 1',
	  # 'price': '100',
	  # etc...
	# }
	'status': 'product status' ,# by default its ‘approved’
	'expire_date': 'product expire date',
	'subcategory': 'subcategory id',
	'photos': {
		'image 1 name' : 'image 1 url',
		'image 2 name' : 'image 2 url'
		# etc....
	}},

'errorPhotos':{
	# Object,
	# photos that rejected

	'image name': image,
	#etc...
}}
```

- note : two main Keys in returned Data: 

1-  data

2-  errorPhotos

------------------------------------


2- get user products

 method: GET,
 
endpoint : /product/get/user/,

required query params : 
```
{

'user':'user token', 

'status':'product status'

}
```

product status have to be one of these:

```
    ('approved),
    ('declined'),
    ('pending'),
    ('sold'),
    ('Deleted')
```

if u want to get all products with all status

```
set status param to 'all'
```

```python
# get data + serialize it
if data['status'] != 'all':
  products = Product.objects.filter(
  user=data['user'], status=data['status'])
else:
  products = Product.objects.filter(user=data['user'])
```
--
returned data :
```
{

date: product created date,

user: user id,

expire_date : product expire date,

status: product status,

category: category id,

subcategory : subcategory id,

fields : product fields , OBJECT!

}

```
3- get products by specific category 

method : GET

endpoint : /product/get/category/

required query params:

'category': category id


returned data :
```
[

list with all products with that category

]
```


4- get products by subcategory 

method : GET

endpoint : product/get/category/sub/

query params:

subcategory : subcategory id 

returned data : 
```
[
list with all products with that subcategory
]
```

5- get product data by product id

method : GET

endpoint : /product/get/id/

query params:

id : product id

returned data:

```
{
normal product data here,
}
```

6- update product fields data

method : POST

endpoint : /product/update/fields/

data to post :

product : product id,

fields  : ALL product fields even the ones u dont want to update

-note- :

* the new fields that u wanna update has to match with product category fields

or u gonna get this error: 
```
{'error': 'Product Fields Does not match with its category'}, status=status.HTTP_400_BAD_REQUEST}
```
returned data :
```
{
product with new fields data
}
```

7- update product status

method : POST

endpoint : /product/update/status/

data to post :
```
{
'status': new product status,
'product': prooduct Id
}
```
-note- :

product status : 

```
    ('approved),
    ('declined'),
    ('pending'),
    ('sold'),
    ('Deleted')
```

returned data : 
{
normal product data + status updated
}



8- update product expire date

every single product has a expire_date

when current date >= expire_date

the user have to update his product expire date

to do it :

method : POST

endpoint : /product/update/expire/

data to post :
```
{
'api_key': api key,
'product': product id
}
```
returned data :
```
{
'updated': prodcut new expire date!
}
```


9- get product comments

method : GET

endpoint : /product/get/comments/

query params : 'product' : product id,

returned data :
```
[
{
'user': user id , the user who create this product,
'description': the context,
'product': product idو
'id': comment id
},
etc....
]
```


10- update product photos  (under test)

-be careful here-

method : PUT

endpoint : /product/update/photos/

data to post :
```
{
'product':product id,
'photos': Object with photo name as Key, the image it self as value
}
```

example for photos object: 

```python
'photos': {
	'image1':image.png,
	'image2':anotherimage.png,
	# etc...
	}
```


returned data :

```python
{
'putPhotos': photosDict # => photos that successfully stored -approved- , Object
'errorPhotos': errorPhotos # => photos that has errors -rejected- , Object
}
```



11 - delete product photos (under test) :

-be careful here-

method : DELETE

endpoint : /product/update/photos/

data to post : 

```
{
'product': product id,
'photos': list of photos names that u want to delete !!
}
```

-- photos value type have to be LIST! --

example of photos LIST: 

```python
[
'image1.png',
'image2.png'
# etc...
]
```

returned data : 

```
{
'deletedPhotos': deletedPhotos, # => deleted photos Names -approved- , List
'errorPhotos': errorPhotos # =>  photos that has errors -rejected- , Object
}
```


# 2- category endpoints 

1- get all categories :

many = True

endpoint : category/get/all

method : GET

query params : api_key

returned data :
```
[
{
'name':category name,
'photo': category photo link,
'status': category status, # always 'appear'
'fields' : category fields  , Object
},
etc....
]
```


2- get category fields -!- :

method : GET,

endpoint : category/get/fields

query params : 'api_key' ,    'category': category id

returned data : 
```
{
category fields
}

# example : 
{
'title': 'required',
'price': 'required',
'address': 'required'
}

# always the value for every key is  : 'required'
# its just initial value doesnt make ant sense! 
```

3- get specific category data :

many = False

method : GET

query params : 'api_key',  'category': category id

endpoit : /category/get/id/

returned data : 
```
{
'name': category name,
'photo': category photo link,
'status': 'approved',
'fields' : Object
}
```


# 3- Sub Categories endpoint

1- get all subcategories 

method : GET

query params : api_key,

endpoint : /category/sub/get/all/

returned data : 
```
[
{
'mainCategory': main category id ,
'name': sub category name,
'photo': sub category photo
}
etc...
]
```

2- get specific sub category data :

method : GET

endpoint : /category/sub/get/id/

query params : 'api_key' , 'subcategory' : subcategory id,

returned data : 
```
{
'mainCategory': main category id ,
'name': sub category name,
'photo': sub category photo
}
```

# 4- User Authencation

1- login : 

method : POST

endpoit : /user/login/

data to post :

{

'username': user username,

'password': user password,

}

returned data :

```
{
'user': user.id, 
'username': user.username, 
'name': user.name, 
'contact': user.contact, 
}
```

- username can be :

-1 user email

-2 user phone number

-3 normal username

because some users may registerd by their email or phone number.

but in database it will stored as 'username'

the most important thing here is -user id-

2- register -sign up-

method : POST

endpoint : /user/register/

data to post : 
```
{
'username': its the method how user wants to login - it can be email or phone number - , have to be unique
'password': user password it have to be atleast 8 digits,
'name': 'full user name',
'contact': user contact number -int-
}
```

returned data :
```
{
'username': username, 
'name': name, 
'contact': contact, 
'user': user.id
}
```

3- update user data


method : POST

endpoint : /user/update/

data to post : 
```
{
'email',
'contact',
'name', 
'user'
}
```

returned data : 
```
{
'user': user token,
'username': user.username, 
'email': user.email, 
'name': user.name, 
'contact': user.contact, 
}
```

- update password 


user can only update his password by knowing his old password

to -reset password- he have to contact with admin to reset it'

endpoint : /user/change/password

method : post

data to post :

```
{
	'username', 
	'oldpassword', 
	'newpassword'
}
```

returned data :
```
{
normal user data
}
```

4- get User Token

method : POST

endpoint : /token

data to post :

```
{
'username':username,
'password': password
}
```
returned data : 

```
{
'refresh': Refresh Token,
'access' : Accress Toekn,
}
```

note - Access Token expire data : 100 weeks
nore - Refresh Token expire date : 1 day


5- Get User Details

endpoint : user/get/details

method : GET

query params : user token

returned data :

```
{
	'username': user.username,
	'name': user.first_name,
	'contact': user.contact,
	'user': user.id,
	'token': token
}
```

# 5- about 

to get about app data :

method : GET

endpoint : /about/

query params : 

-No query params-

returned data : 
```
{
'icon_link': in app icon link,
'privacy_policy' : privacy policy page text,
'about_us': about us page text,
'contact_number' : admin contact number,
'whatsapp_number' : admin whatsapp number,
'agree_text' : agreement page text,
'payment_info_text': payment info page text,
'payment_info_link' : payment info link,
'product_expire_days' : after (var) days the product gonna expire
}
```

- note :
lets say the admin dont want 'payment_info_link'

the value gonna be then '1'

1 = null

anything here its value is '1' that means Null

# comments

1- create new comment :

method : PUT

endpoint : /product/comment

data to post :

```
{
'token': user token,
'description': comment context,
'product': product id,
'id': comment id,
}
```

returned data :
```
{
'user': user id,
'description': comment context,
'product': product id,
'id': comment id ,
}
```

2- delete comment : 

method : DELETE

endpoint : /product/comment/
data to post :
```
{
'comment': comment id
}
```

returned data : 
```
{
'deleted'
}
```

# Replaies

-1 create new replay:

endpoint : product/comment/replay

method : PUT

data to post: 

```
{
'user': user token,
'product': product id,
'comment': comment id,
'description': description
}
```
returned data : 

{

normal comment data

}


2- delete replay 

method : DELETE

endpoint : product/comment/replay

data to post :

```
{
'replay': replay id
}
```

# ADS - free banner -

1- get all ads

method : GET

endpoint : /ads/

query params : 

```
{
'api_key': api key only,
}
```

returned data : 
```python
[
	{
		'name': 'ad name ',
		'status': 'ad status',
		'photo': 'ad photo link',
		'date': 'creation date',
		'expire_date' : 'ad expire date',
		'contact': 'ad contact number',
	},
	# etc...
]
```

-returned data type : List-



# Notification

1- create new Notification

endpoint : user/notifications

method : POST

data to post : 

```
{
'user': user Token,
'body': notification text
}
```

retured data : 
```
{
'user': user details,
'body': notification text,
'date': date,
'id': notification id
}
```


2- delete notification 

method : DELETE

endpoint : user/notifications

data to post : 


delete specific notification :

```
{
'id' : notification,
}
```

clear all user notidication : 

```
{
'user':user Token
}
```

returned data :

```
{'deleted'}
```

3- get user notification

method : GET

query params : 
```
{
'user': user Token
}
```

returned data :

```

[
	'user': user details,
	'body': notification text,
	'date': date,
	'id': notification id
	},
	# ... etc
]

```

# ERRORS :

1- ``` {'error': 'bad request'} ```

this error appreas when the given data dont match with the required data

to solve it check the data u have posted

2- ```{'error': 'bad API_KEY'}```

to solve this just check the api key

3- ```{'error': 'Product Fields Does not match with its category'}```

the code :

```python 
    fields = data[‘fields’]
    category = Category.objects.get(id=data[‘category’])
    # convert STR to DICT/JSON
    Jfields = json.loads(fields)
 
    # check Product Fields
    if Jfields.keys() == category.fields.keys():
	do some stuff
    else:
	return {{'error': 'Product Fields Does not match with its category'}}
```

in this case the products fields u have sent are not match with its category 

to solve this :

1- check category fields -endpoint : /category/get/fields/-

2- check the given product fields

the keys have to be matched

4- -Username or Password are wrong-, -Username doesnt exists-

it depends on auth, user bad input
