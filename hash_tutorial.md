# Hashes

Hashes are like arrays.  They are a data structure, and therefore store data.

### New Hashes

New, empty hashes can be created the following ways:

```ruby
hash_one = Hash.new
hash_two = {}
hash_three = Hash.new(true)
hash_four = Hash.new { |hash,key| hash[key] = Array.new }
```

Nonempty hashes can be created like this:

```ruby
hash_five = { :key => :value, :key_two => :value_two }
```

The symbol above, `=>`, is called the 'Hash Rocket'.  Whatever comes to the left of it is the key, and whatever comes to the right of it is called the value.  The hash rocket is an older way of notating this association, and the more 'modern' version looks like this:

```ruby
hash_six = { key: :value, key_two: :value_two }
```

Note, `key` and `key_two` here are assumed to be symbols, that is `key:` is equivalent to `:key =>`.  The modern way of writing key-value pairs works only if the keys are symbols.  The values don't have to be, so you might see this:

```ruby
hash_seven = { key: "value", key_two: "value_two" }
```

Where arrays have a integer based indexing system, hashes are much more flexible.  You can use any object to index your hash.  Things like integers, strings, arrays, symbols, and even other hashes are all valid keys.

```ruby
hash_one[5] = true # hash_one == { 5 => true }
hash_one['this'] = 'that' # hash_one == { 5 => true, 'this' => 'that' }
hash_one[:one] = 5 # hash_one == { 5 => true, 'this' => 'that', :one => 5 }
```

We were just assigning values to keys, and when you access a hash by a stored key, you get a stored value.  However, if there is no stored value, a hash will still return something.  That's called the default value.

```ruby
hash_one[:two] # => nil
hash_two[5] # => nil
hash_three['catoblepas'] # => true
```

From above, `hash_four` is a bit funny, thanks to the block we passed to the new method for the hash class.  The code inside is actually executed anytime an unstored key is accessed.  

```ruby
hash_four[:ennui] # => []
hash_four # { :ennui => [] }
```

So when we try to access the unstored key, `:ennui`, the block executes and assigns to `hash_four` the pair `:ennui` and `Array.new`, so after accessing the key `:ennui`, we are storing a new array at that address.

Sometimes we want to know what keys are being stored in our hash, maybe to make note of them so that we can use them later to access the values.  Or perhaps we want to access the values, as well, since that's the data that's being stored.  We have instance methods: keys *and* values.  Using hash_one from above:

```ruby
hash_one.keys # [ 5, 'this', :one ]
hash_one.values # [ true, 'that', 5 ]
```

Why might we want to use a hash instead of an array?  One reason is the fact that you are pairing up information that can be accessed by keys which describe the data rather than indices which are sometimes meaningless.

Watch out!  Nested hashes ahead!!

```ruby
user_list = {
  :user_1 => {
    :name => "Matthew",
    :email => "short.matthew.f@gmail.com",
    :age => 35
  },
  :user_2 => {
    :name => "James",
    :email => "james.smith@gmail.com",
    :age => 27
  }
}
```

Here :user_1 and :user_2 both point to more hashes.  That means that user_list[:user_1] is another hash, and so we can access it's values by addressing the keys stored therein. 

```ruby
user_list[:user_1] # => { :name => "Matthew", :email => "short.matthew.f@gmail.com", :age => 35 }
user_list[:user_1][:name] # "Matthew"
user_list[:user_2][:age] # 27
```

Another important reason to use a hash over an array is the speed of finding something.  To find something in an array you might have to look at each and every element in the array.  

If we ask if `[1, 3, 21, 8, 9, 16, 33, 58, 14]` contains the number 15, we would scan from left to right checking if each entry is equal to 15.  This is somewhat slow.

However, if we ask if `{ 1 => true, 3 => true, 21 => true, ... }`, then asking if 15 is a key is as simple as trying to access it.  `my_hash[15]` will return the default value of `nil`.

What are some other super useful hash instance methods?

### Hash#delete
`delete` takes a key as an argument, returns the value for that key, and removes the key-value pair from the hash.

```ruby
a = { :one => 1, :two => 2 }
a.delete(:one) # => 1
a # => { two => 2 }
```

### Hash#merge

`merge` takes a second hash as an argument, and returns a hash that has both sets of keys and values, giving preference two the second hash if there are duplicate keys.

```ruby
a = { :one => 1, :two => 2 }
b = { :two => :two, :three => 3 }
a.merge(b) # => { one: 1, two: two, three: 3 }
```

Lastly, since you're linking two pieces of data as a pair, hashes are incredibly useful as arguments for methods.  In fact, they're so useful you will frequently encouter THE OPTIONS HASH.

```ruby
def my_method(name, options = {})
  if options[:greet] == true && options[:reverse] == true
    puts "Hello, #{name.reverse}"
  elsif options[:greet] == true
    puts "Hello, #{name}"
  else
    puts "I'm not talking to you right now."
  end
end
```

So, let's dissect what we expect as input first.  Here, my_method takes two inputs, a name and an options hash.  options = {} will allow us to only call the method with a name, and provide the empty hash to the variable options if we do.  Here's are some valid inputs:

```ruby
my_method("Matthew") # I'm not talking to you right now.
my_method("Matthew", { greet: false }) # I'm not talking to you right now.
my_method("Matthew", { greet: true }) # Hello, Matthew
```

What happens if we try to pass in a key pair value that isn't being tested for?

```ruby
my_method("Matthew", { greet: true, speak_gibberish: true }) # Hello, Matthew
```

Nothing.  Our method only accessed the hash looking for the values associated to the keys `:greet` and `:reverse`.  If the key-value pairs for `:greet` or `:reverse` are missing, then the hash returns the default value of `nil`, which is falsy enough to act as `false` for conditionals.

Additionally, if your last parameter for a method is an options hash, as above, you can call it by just listing key-value pairs consecutively as inputs.  This means that:

```ruby
my_method("Matthew", { greet: true, reverse: true }) # Hello, wehttaM
```
is the same as

```ruby
my_method("Matthew", greet: true, reverse: true) # Hello, wehttaM
```

This flexibility is one reason we love options hashes, and also one reason why you'll see them sprinkled so generously through all rails code and examples. 


