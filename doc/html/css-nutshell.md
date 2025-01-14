# CSS
## Basic rules

 1. Niemals zusätzliche Klassen für das Grid-System definieren, weil das sonst das ganze layout durcheinander bringen könnte.
 2. Das Styling in spezifische und generelle aufteilen. Die Generellen sind am Anfang und spezischen zum Ende.
 3. Die Reihenfolge der Pseudeklassen: 
    1. :link
    2. :visited
    3. :active
    4. :hover

 ## Tips
 Mit Pseudeklassen kann man die geraden Elemente nach link rücken lassen und die ungeraden nach rechts.

``` css
/* Annahme wir haben 4 Boxen. Zwei links und zwei rechts. */
#myid .row > .col-widhth-3:nth-of-type(even) > .my-box {
    margin-left: 1rem;
}

#myid .row > .col-widhth-3:nth-of-type(odd) > .my-box {
    margin-right: 1rem;
}
```

## Fonts

Due to the DSI law not connection to other servers is allowed and data used on the web site, incl. fonts need to be preset locally. 

The fonts can be defined and imported in css
``` css
/*  */
@font-face {
    font-family: 'Name';
    font-style: normal;
    /*  this is the normal size */
    font-weight: 400; 
    src: url('../');
    src: local('xyz'),
        url('xyz'),
        url('xyz'),
        url('xyz'),
}
```

## Pseudo-Klassen
Die Pseudo-Klassen in der sogenannten **LVHA**-Reihenfolge verarbeitet werden sollten:

- :link (Links, die noch nicht besucht wurden)
- :visited (Bereits besuchte Links)
- :hover (Wenn der Mauszeiger über dem Link ist)
- :active (Während der Link geklickt wird)