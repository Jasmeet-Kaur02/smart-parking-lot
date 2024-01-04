# smart-parking-lot

### Database Schema 

1. parking_blocks table -
   1. block_code
   2. available_floors

2. floors table -
   1. floor_number
   2. floor_height
   3. available_slots
   4. block_id

3. slots table -
   1. slot_number
   2. slot_space
   3. floot_id
   4. charges_per_minute
   5. is_reserved
  
4. customers table
   1. firstName
   2. lastName
   3. email
   4. phone_number

5. vehicles table
   1. vehicle_number
   2. size
   3. height
   4. type
  
6. reservations table
   1. customer_id
   2. vehicle_id
   3. slot_id
   4. status  // waiting, parked, exited, cancelled
   5. entry_time
   6. exit_time

7. payments table
   1. customer_id
   2. vehicle_id
   3. slot_id
   4. status
   5. payment_method
   6. total_fees
  
### Algorithm to assign parking slots 

Step 1. Search for available floors with a height greater than or equal to the given vehicle height where the 'available_slots' column is not 0.


Step 2. From the available floors that satisfy the previous condition, search for slots with an space equal to the vehicle size where 'is_reserved' is false.


Step 3. If slot has been found, change "is_reserved" column to true for that slot and for it's associated floor record, decrease "available_slots" column value by 1.


Step 4. Check if the value of 'available_slots' for that floor becomes zero. If it does, then for its associated block record, decrease the 'available_floors' column value by 1.


Step 5. Add a new record with customer_id, vehicle_id, slot_id and waiting status in the reservation table.


Step 6. If, within 30 minutes, the vehicle does not enter the slot, then change the status to 'cancelled' for the reservation record of that slot, and change 'is_reserved' to true for that slot. 
        Afterward, increase the value of 'available_slots' for its associated floor record by 1.

        
Step 7. Before updating the 'available_slots' of that floor, check if its current value is zero. If it is, also increase the value of 'available_floors' for its 
        associated block record by 1.

        
Step 8. In step 6, update the status of the reservation slot record from 'pending' to 'parked' only if the vehicle parks in the slot within 30 minutes.


#### Note - 
While assigning a slot to the vehicle, transaction can be used to handle concurrency during vehicle entries."

### Logic to calculate total parking fees 
1. When the vehicle exits from the parking slot, update the exit time and status to "exited" for that reserved slot record in the reservation table,
   and change the 'is_reserved' column of the slot to false.
2. Retrieve the per-minute charges for that slot, and calculate the total number of minutes the vehicle stayed in the slot from its entry and exit times.
   Multiply this duration by the total charges per minute of the slot."
3. In the step 1 while updating slot "is_reserved" column, increase the value of 'available_slots' for its associated floor record by 1.
4. In the step 3 Before updating the 'available_slots' of that floor, check if its current value is zero. If it is, also increase the value of 'available_floors' for its 
   associated block record by 1.
5. Add a new record in the payment table with customer_id, vehicle_id, slot_id, total_fees, pending status and payment_method null.


