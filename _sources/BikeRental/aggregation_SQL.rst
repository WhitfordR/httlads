Aggregation or Group By
=======================

One very powerful features of SQL is that it allows us to create summary
information by grouping rows together. For example, we could ask
ourselves how many bike trips were taken for each subscriber type, and
which subscriber type has the most bike trips?

.. code:: ipython3

    %%sql
    SELECT
      member_type, COUNT(*)
    FROM
      trip_data
    GROUP BY
      member_type
    ORDER BY
      COUNT(*) DESC
    LIMIT
      10



.. parsed-literal::

     * sqlite:///bikeshare.db
    Done.




.. raw:: html

    <table>
        <tr>
            <th>member_type</th>
            <th>COUNT(*)</th>
        </tr>
        <tr>
            <td>Member</td>
            <td>979814</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>246949</td>
        </tr>
        <tr>
            <td>Unknown</td>
            <td>4</td>
        </tr>
    </table>



``GROUP BY subscriber_type`` takes all the rows with a given
subscriber_type and produces a single row in the result. This means that
we need to tell SQL how we want to combine the other columns’ values
into a single row. The above example uses ``COUNT(*)`` which reports of
the number of rows that were combined.

Aggregating the values for ``subscriber_type`` is not hard, since
they’re all the same, SQL just gives us a single copy of the publisher
name. Other columns, we need to either ignore (causing them to be
omitted from the output) or specify a way to aggregate them.

We must specify an aggregate function for any column that we ``SELECT``
in our query (except the column that we’re grouping by) in order for the
command to succeed. If we don’t specify a way to aggregate the value,
SQL will complain. For example, the following query should fail:

.. code:: ipython3

    %%sql

    SELECT
      member_type, duration
    FROM
      trip_data
    GROUP BY
      subscriber_type
    ORDER BY
      COUNT(*) DESC



.. parsed-literal::

     * sqlite:///bikeshare.db
    (sqlite3.OperationalError) no such column: subscriber_type [SQL: 'SELECT\n  member_type, duration\nFROM\n  trip_data\nGROUP BY\n  subscriber_type\nORDER BY\n  COUNT(*) DESC'] (Background on this error at: http://sqlalche.me/e/e3q8)


Let’s go back briefly to the first query in the Aggregation section. The
top result was the count of bike trips for member_type ``Member``:

=========== ========
member_type COUNT(*)
=========== ========
Member      979814
=========== ========

If you’d like to get a more granular break down of the count, you may
specify multiple columns to aggregate within the ``GROUP BY`` clause,
for example: further breakdown the aggregate count by the start station
IDs:

.. code:: ipython3

    %%sql

    SELECT
      member_type, start_station, count(*)
    FROM
      trip_data
    WHERE
      member_type = 'Casual'
    GROUP BY
      member_type, start_station
    ORDER BY
      COUNT(*) DESC
    LIMIT
      20



.. parsed-literal::

     * sqlite:///bikeshare.db
    Done.




.. raw:: html

    <table>
        <tr>
            <th>member_type</th>
            <th>start_station</th>
            <th>count(*)</th>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31200</td>
            <td>10922</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31217</td>
            <td>10912</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31235</td>
            <td>9829</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31219</td>
            <td>8736</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31225</td>
            <td>7180</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31228</td>
            <td>6111</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31222</td>
            <td>5943</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31215</td>
            <td>5224</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31201</td>
            <td>4991</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31218</td>
            <td>4960</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31237</td>
            <td>4906</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31232</td>
            <td>4905</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31623</td>
            <td>4853</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31205</td>
            <td>4751</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31613</td>
            <td>4162</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31212</td>
            <td>4029</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31238</td>
            <td>3920</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31104</td>
            <td>3908</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31203</td>
            <td>3772</td>
        </tr>
        <tr>
            <td>Casual</td>
            <td>31204</td>
            <td>3675</td>
        </tr>
    </table>



Great! Now that you’re familiar with how to aggregate data using SQL
query by using ``COUNT()`` as your aggregation function, let’ take a
look at other aggregation functions.

There are `many such
functions <https://www.postgresql.org/docs/9.5/functions-aggregate.html>`__.
Some common ones include:

-  ``SUM``: To add the values together
-  ``AVG``: To compute the mean of the values
-  ``MIN`` or ``MAX``: To compute the minimum and maximum respectively

So we could for example compute the **total** number of minutes of all
bike trips for all subscriber types

.. code:: ipython3

    %%sql

    SELECT
      member_type, SUM(duration)
    FROM
      trip_data
    GROUP BY
      member_type
    LIMIT
      10



.. parsed-literal::

     * sqlite:///bikeshare.db
    Done.




.. raw:: html

    <table>
        <tr>
            <th>member_type</th>
            <th>SUM(duration)</th>
        </tr>
        <tr>
            <td>Casual</td>
            <td>687530197</td>
        </tr>
        <tr>
            <td>Member</td>
            <td>759503541</td>
        </tr>
        <tr>
            <td>Unknown</td>
            <td>3434</td>
        </tr>
    </table>



Exercise 3
----------

Compute the average duration of bike trips for each starting station id
and list the results in order of highest average to lowest average.

.. reveal:: bikes_agg
    :instructoronly:

    .. code:: ipython3

        %%sql

        SELECT
        start_station, AVG(duration)
        FROM
        trip_data
        GROUP BY
        start_station
        ORDER BY
        AVG(duration) DESC
        LIMIT
        10

.. raw:: html

    <table>
        <tr>
            <th>start_station</th>
            <th>AVG(duration)</th>
        </tr>
        <tr>
            <td>31806</td>
            <td>40669.5</td>
        </tr>
        <tr>
            <td>31052</td>
            <td>4325.0</td>
        </tr>
        <tr>
            <td>31705</td>
            <td>3787.787878787879</td>
        </tr>
        <tr>
            <td>31262</td>
            <td>3563.8636363636365</td>
        </tr>
        <tr>
            <td>31704</td>
            <td>3550.0</td>
        </tr>
        <tr>
            <td>31703</td>
            <td>3134.6492146596856</td>
        </tr>
        <tr>
            <td>31266</td>
            <td>2906.0</td>
        </tr>
        <tr>
            <td>31217</td>
            <td>2431.043944420405</td>
        </tr>
        <tr>
            <td>31016</td>
            <td>2414.4292185730465</td>
        </tr>
        <tr>
            <td>31235</td>
            <td>2368.5348916450866</td>
        </tr>
    </table>

* What is the userid of the Member with the most rides?

* what is the userid of the Casual rider with the most rides?

* What is the station that has the most rides that start and end at the same station?

**Lesson Feedback**

.. poll:: LearningZone_10_3
    :option_1: Comfort Zone
    :option_2: Learning Zone
    :option_3: Panic Zone

    During this lesson I was primarily in my...

.. poll:: Time_10_3
    :option_1: Very little time
    :option_2: A reasonable amount of time
    :option_3: More time than is reasonable

    Completing this lesson took...

.. poll:: TaskValue_10_3
    :option_1: Don't seem worth learning
    :option_2: May be worth learning
    :option_3: Are definitely worth learning

    Based on my own interests and needs, the things taught in this lesson...

.. poll:: Expectancy_10_3
    :option_1: Definitely within reach
    :option_2: Within reach if I try my hardest
    :option_3: Out of reach no matter how hard I try

    For me to master the things taught in this lesson feels...