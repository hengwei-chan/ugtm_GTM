Visualization examples
======================

GTM visualization examples from following datasets:
        - S-curve (from `sklearn s-curve example <https://scikit-learn.org/stable/auto_examples>`_)
        - Severed sphere (from `sklearn severed sphere example <https://scikit-learn.org/stable/auto_examples/manifold/plot_manifold_sphere.html#sphx-glr-auto-examples-manifold-plot-manifold-sphere-py>`_)
        - Hand-written digits (from `sklearn hand-written digits example <https://scikit-learn.org/stable/auto_examples/manifold/plot_lle_digits.html#sphx-glr-auto-examples-manifold-plot-lle-digits-py>`_)

S-curve
-------

.. altair-plot::

        from ugtm import eGTM,eGTR
        import numpy as np
        import altair as alt
        import pandas as pd
        from sklearn import datasets
        from sklearn import metrics
        from sklearn import model_selection
        from sklearn import manifold

        X,y = datasets.make_s_curve(n_samples=1000, random_state=0)
        man = manifold.TSNE(n_components=2, init='pca', random_state=0)
        tsne = man.fit_transform(X)
        man = manifold.MDS(max_iter=100, n_init=1, random_state=0)
        mds = man.fit_transform(X)
        man = manifold.LocallyLinearEmbedding(n_neighbors=20, n_components=2,
                                              eigen_solver='auto',
                                              method="standard",
                                              random_state=0)
        lle = man.fit_transform(X)

        # Construct GTM 
        gtm_means = eGTM(m=2, model="means").fit_transform(X)
        gtm_modes = eGTM(m=2, model="modes").fit_transform(X)

        dgtm_modes = pd.DataFrame(gtm_modes, columns=["x1", "x2"]) 
        dgtm_modes["label"] = y 

        gtm_modes = alt.Chart(dgtm_modes).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:Q']
        ).properties(title = "GTM (modes)", width = 100, height = 100)

        dgtm_means = pd.DataFrame(gtm_means, columns=["x1", "x2"]) 
        dgtm_means["label"] = y 
                                                                       
        gtm_means = alt.Chart(dgtm_means).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:Q']
        ).properties(title = "GTM (means)", width = 100, height = 100)

        #Construct activity landscape 
        gtr = eGTR(m=2) 
        gtr = gtr.fit(X,y)

        dfclassmap = pd.DataFrame(gtr.optimizedModel.matX, columns=["x1", "x2"]) 
        dfclassmap["label"] = gtr.node_label

        # Classification map
        gtr = alt.Chart(dfclassmap).mark_square().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2', 'label:Q'],
            #opacity='density'
        ).properties(title = "GTM landscape",width = 100, height = 100)

        dtsne = pd.DataFrame(tsne, columns=["x1", "x2"]) 
        dmds = pd.DataFrame(mds, columns=["x1", "x2"])
        dlle = pd.DataFrame(lle, columns=["x1", "x2"])  
        dtsne["label"] = y
        dmds["label"] = y
        dlle["label"] = y

        tsne = alt.Chart(dtsne).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:Q']
        ).properties(title = "t-SNE", width = 100, height = 100)

        mds = alt.Chart(dmds).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:Q']
        ).properties(title = "MDS", width = 100, height = 100)

        lle = alt.Chart(dlle).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:Q']
        ).properties(title = "LLE", width = 100, height = 100)


        gtm = gtm_means | gtm_modes | gtr
        others = tsne | mds | lle

        alt.vconcat(gtm, others)


Severed sphere
--------------

.. altair-plot::

        from ugtm import eGTM,eGTR
        import numpy as np
        import altair as alt
        import pandas as pd
        from sklearn import datasets
        from sklearn import metrics
        from sklearn import model_selection
        from sklearn import manifold
        from sklearn.utils import check_random_state

        random_state = check_random_state(0)
        p = random_state.rand(1000) * (2 * np.pi - 0.55)
        t = random_state.rand(1000) * np.pi

        # Sever the poles from the sphere.
        indices = ((t < (np.pi - (np.pi / 8))) & (t > ((np.pi / 8))))
        x, y, z = np.sin(t[indices]) * np.cos(p[indices]), \
            np.sin(t[indices]) * np.sin(p[indices]), \
            np.cos(t[indices])

        X = np.array([x, y, z]).T

        y = p[indices]

        man = manifold.TSNE(n_components=2, init='pca', random_state=0)
        tsne = man.fit_transform(X)
        man = manifold.MDS(max_iter=100, n_init=1, random_state=0)
        mds = man.fit_transform(X)
        man = manifold.LocallyLinearEmbedding(n_neighbors=10, n_components=2,
                                              eigen_solver='auto',
                                              method="standard",
                                              random_state=0)
        lle = man.fit_transform(X)

        # Construct GTM 
        gtm_means = eGTM(m=2, model="means").fit_transform(X)
        gtm_modes = eGTM(m=2, model="modes").fit_transform(X)

        dgtm_modes = pd.DataFrame(gtm_modes, columns=["x1", "x2"]) 
        dgtm_modes["label"] = y 

        gtm_modes = alt.Chart(dgtm_modes).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:Q']
        ).properties(title = "GTM (modes)", width = 100, height = 100)

        dgtm_means = pd.DataFrame(gtm_means, columns=["x1", "x2"]) 
        dgtm_means["label"] = y 
                                                                       
        gtm_means = alt.Chart(dgtm_means).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:Q']
        ).properties(title = "GTM (means)", width = 100, height = 100)

        #Construct activity landscape 
        gtr = eGTR(m=2) 
        gtr = gtr.fit(X,y)

        dfclassmap = pd.DataFrame(gtr.optimizedModel.matX, columns=["x1", "x2"]) 
        dfclassmap["label"] = gtr.node_label

        # Classification map
        gtr = alt.Chart(dfclassmap).mark_square().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2', 'label:Q'],
            #opacity='density'
        ).properties(title = "GTM landscape",width = 100, height = 100)

        dtsne = pd.DataFrame(tsne, columns=["x1", "x2"]) 
        dmds = pd.DataFrame(mds, columns=["x1", "x2"])
        dlle = pd.DataFrame(lle, columns=["x1", "x2"])  
        dtsne["label"] = y
        dmds["label"] = y
        dlle["label"] = y

        tsne = alt.Chart(dtsne).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:Q']
        ).properties(title = "t-SNE", width = 100, height = 100)

        mds = alt.Chart(dmds).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:Q']
        ).properties(title = "MDS", width = 100, height = 100)

        lle = alt.Chart(dlle).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:Q',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:Q']
        ).properties(title = "LLE", width = 100, height = 100)


        gtm = gtm_means | gtm_modes | gtr
        others = tsne | mds | lle

        alt.vconcat(gtm, others)


Hand-written digits
-------------------

.. altair-plot::

        from ugtm import eGTM,eGTC
        import numpy as np
        import altair as alt
        import pandas as pd
        from sklearn import datasets
        from sklearn import metrics
        from sklearn import model_selection
        from sklearn import manifold
        from sklearn.utils import check_random_state

        
        digits = datasets.load_digits(n_class=6)
        X = digits.data
        y = digits.target

        man = manifold.TSNE(n_components=2, init='pca', random_state=0)
        tsne = man.fit_transform(X)
        man = manifold.MDS(max_iter=100, n_init=1, random_state=0)
        mds = man.fit_transform(X)
        man = manifold.LocallyLinearEmbedding(n_neighbors=20, n_components=2,
                                              eigen_solver='auto',
                                              method="standard",
                                              random_state=0)
        lle = man.fit_transform(X)

        # Construct GTM 
        gtm_means = eGTM(m=2, model="means").fit_transform(X)
        gtm_modes = eGTM(m=2, model="modes").fit_transform(X)

        dgtm_modes = pd.DataFrame(gtm_modes, columns=["x1", "x2"]) 
        dgtm_modes["label"] = y 

        gtm_modes = alt.Chart(dgtm_modes).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:N',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:N']
        ).properties(title = "GTM (modes)", width = 100, height = 100)

        dgtm_means = pd.DataFrame(gtm_means, columns=["x1", "x2"]) 
        dgtm_means["label"] = y 
                                                                       
        gtm_means = alt.Chart(dgtm_means).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:N',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:N']
        ).properties(title = "GTM (means)", width = 100, height = 100)

        #Construct activity landscape 
        gtc = eGTC() 
        gtc = gtc.fit(X,y)

        dfclassmap = pd.DataFrame(gtc.optimizedModel.matX, columns=["x1", "x2"]) 
        dfclassmap["label"] = gtc.node_label

        # Classification map
        gtc = alt.Chart(dfclassmap).mark_square().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:N',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2', 'label:N'],
            #opacity='density'
        ).properties(title = "GTM class map",width = 100, height = 100)

        dtsne = pd.DataFrame(tsne, columns=["x1", "x2"]) 
        dmds = pd.DataFrame(mds, columns=["x1", "x2"])
        dlle = pd.DataFrame(lle, columns=["x1", "x2"])  
        dtsne["label"] = digits.target_names[y]
        dmds["label"] = digits.target_names[y] 
        dlle["label"] = digits.target_names[y] 

        tsne = alt.Chart(dtsne).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:N',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:N']
        ).properties(title = "t-SNE", width = 100, height = 100)

        mds = alt.Chart(dmds).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:N',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:N']
        ).properties(title = "MDS", width = 100, height = 100)

        lle = alt.Chart(dlle).mark_circle().encode(
            x='x1',
            y='x2',
            color=alt.Color('label:N',
                            scale=alt.Scale(scheme='viridis')),
            size=alt.value(50),
            tooltip=['x1','x2','label:N']
        ).properties(title = "LLE", width = 100, height = 100)


        gtm = gtm_means | gtm_modes | gtc
        others = tsne | mds | lle

        alt.vconcat(gtm, others)
