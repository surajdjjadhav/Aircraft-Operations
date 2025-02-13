# Fuel Optimization & Customer Retention for Aircraft Operations



[Business Problem and Analysis available in this file](https://github.com/surajdjjadhav/Aircraft-Operations/blob/main/Aircraft%20Operation.pdf)


## Tools Used :
1.[ Python](https://github.com/surajdjjadhav/Aircraft-Operations/blob/main/airline.ipynb)
2. sqlite
3. PowerBI

### sql Query used in data manupulation & data fetching in power bi 

#### 1st Table 
  SELECT  
      f.aircraft_code,  
      b.book_date,  
      f.scheduled_departure,  
       avg(t.amount) as ticket_price,
      SUM(t.amount) AS total_revenue,  
      t.fare_conditions,  
      ts.passenger_id,  
      COUNT(*) AS total_tickets  
  FROM ticket_flights t  
  JOIN flights f ON t.flight_id = f.flight_id  
  JOIN tickets ts ON t.ticket_no = ts.ticket_no  
  JOIN bookings b ON ts.book_ref = b.book_ref  
  GROUP BY f.aircraft_code, b.book_date, f.scheduled_departure, ts.passenger_id, t.fare_conditions;

#### 2nd Table 
  select
           t.passenger_id ,<br>
           SUM(tf.amount) AS total_investment,<br>
           COUNT(tf.flight_id) AS total_travels ,<br>
           julianday('2017-12-31') - MAX(julianday(substr(b.book_date, 1, 10))) AS recency <br>
           from tickets t inner join ticket_flights tf on t.ticket_no=tf.ticket_no<br>
           inner join bookings b on t.book_ref=b.book_ref <br>
           group by passenger_id <br>

#### 3rd Table

  with cte as (
                   select <br>
                   f.aircraft_code , f.flight_id , count(*) as total_booking <br>
                   from flights f inner join boarding_passes b on f.flight_id =b.flight_id <br>
                   group by f.aircraft_code , f.flight_id),<br>
                   total_seats as (<br>
                   select aircraft_code , count(seat_no) as total_seats
                   from seats
                   group by  aircraft_code
                          )
                            select 
                            c.aircraft_code , round(avg(c.total_booking) ,2) as total_book , t.total_seats ,
                            round((avg(c.total_booking)/t.total_seats),3) as occupancy_rate 
                            from cte c inner join total_seats t on c.aircraft_code= t.aircraft_code
                            group by c.aircraft_code

