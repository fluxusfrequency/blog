# ActiveRecord interesting code tidbits

## From Examples

```
LOREM.grep(/^\w*$/).sort_by { rand }.first(2).join ' '
```

## ActiveRecord::Reflection

```
r = User.reflect_on_association(:sent_messages)
r.macro #=> :has_many
r.constructable? #=> true
r.has_inverse? #=> false
r.join_keys(1) #=> #<struct ActiveRecord::Reflection::AbstractReflection::JoinKeys key="sender_id", foreign_key="id">
r.check_eager_loadable #=> true
r.foreign_type #=> "sent_messages_type"
r.polymorphic?
r.has_one?
r.belongs_to?
r.nested?
r.quoted_table_name #=> "\"messages\""
r.join_table => "messages_users"
```

## Rails Generator

`lib/rails/generators/active_record/model/templates/model.rb`

## No Touching

```
ActiveRecord::Base.no_touching do
  Project.first.touch  # does nothing
  Message.first.touch  # does nothing
end
```
