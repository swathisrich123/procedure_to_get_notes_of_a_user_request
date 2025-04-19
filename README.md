# procedure_to_get_notes_of_a_user_request

    PROCEDURE proc1(p1 IN VARCHAR2,
                                     p2 IN VARCHAR2,
                                     p3 OUT VARCHAR2) AS
        var1   NUMBER := 0;
        var2 VARCHAR2(32767);
        var3 NUMBER := 50;
        var4 NUMBER;

        CURSOR c1(p1 IN VARCHAR2, p2 IN VARCHAR2) IS
            WITH table2 AS
             (SELECT pack1.get_clob_substr(col1) col1, col2
                FROM table1
               WHERE col3 = p1
                 AND col4 = p2)
            SELECT t1.col1
              FROM table2 t1
             WHERE t1.col2 IN
                   (SELECT MAX(col2)
                      FROM table2 t2
                     WHERE t2.col1 = t1.col1
                     GROUP BY t2.col1)
             ORDER BY t1.col2 DESC;

    BEGIN

        SELECT COUNT(*)
          INTO var1
          FROM (SELECT pack1.get_clob_substr(col1)
                   FROM table1
                  WHERE col3 = p1
                    AND col4 = p2);
        BEGIN
            IF (var1 = 0) THEN

                var2 := NULL;
            ELSIF (var1 = 1) THEN
                SELECT pack1.get_clob_substr(col1)
                  INTO var2
                  FROM table1
                 WHERE col3 = p1
                   AND col4 = p2;
            ELSIF (var1 > 1) THEN
                BEGIN

                    FOR loop1 IN c1(p1, p2)
                    LOOP

                        var2 := var2 || loop1.col1 || chr(10);
                    END LOOP;

                END;

            END IF;

            var2 := escape_special_characters(var2);

        EXCEPTION
            WHEN OTHERS THEN
                var2 := NULL;
        END;

        var2 := get_clob_substr(var2);

        var4 := lengthb(var2);

        IF ((var4 + var3) > 4000) THEN

            var2 := TRIM(substr(var2, 1, (length(var2) - var3)));

        END IF;

        p3 := var2;
    END proc1;
