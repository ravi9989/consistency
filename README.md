# consistency
how to maintain consistency of data when there are multiple writers in the same db

Now the problem is there are two events E1, E2 where they are trying to write data at t1, t2 time respectively. But due to some network issues the event E1 failed to write data into memory, now at t2 the event E2 writes data to memory successfully. after some time E1 get released from issue and writes the data into memory.
so we can clearly see a problem that most recent write event is overidden by old event, How to solve this when we are using multiple systems to write on same memory, how can we achieve consistency?

My idea is to give versioning to the data, Take a counter which is centralised for every system assign the couter value to the event, now when we are write data into memory assign the same counter as a version but before overwritting the memory or record verify wether it is most recent or not if its not update with current event data.

How to get to know that the record is most recent?

when we are applying a write operation on memory/record take the version inthe record compare it with current event version if record having lesser version than current event overwrite the record else do not update 

this is how we can solve this small consistency problem by versioning

```python
COUNTER = 1

def update_record(index, record):

    memory[index] = record
    
    return True
    
def check_most_recent(record, current_event_record):
  
  if record["version"] < current_event_record["version"]:
      
      return False
      
  return True
  
def centralised_counter():
    
    COUNTER += 1
    
    return COUNTER
    
# on arrival of new event 
current_event["version"] = centralised_counter()

index = current_event["index"] # on address where the current event is trying to write

if not memory[index]:
  
  memory[index] = current_event
  
  exit

if check_most_recent(memory[index], current_event):

    update_record(index, current_event)
   
 else:
 
    #no change
```
