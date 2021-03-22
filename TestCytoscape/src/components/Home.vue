<template>
    <div class="home">
        <cytoscape ref="cy" :config="config">
            <cy-element v-for="def in elements"
                        :key="`${def.data.id}`"
                        :definition="def"
                        v-on:mousedown="deleteNode($event, def.data.id)" />
        </cytoscape>
        <button @click="moveThroughWorkflow">Move Through Workflow</button>
    </div>
</template>

<script>
    
    //import cytoscape from 'cytoscape';
    //import edgehandles from 'cytoscape-edgehandles';
    //import popper from 'cytoscape-popper';

    //cytoscape.use(popper);
    //cytoscape.use(edgehandles);

    export default {
        name: 'Home',
        props: {
            msg: String
        },
        data() {
            return {
                config: {
                    layout: {
                        name: 'preset',
                        //boundingBox: { x1: 0 , x2: 100, y1: 0, y2: 100},
                    },
                    style: [
                        {
                            selector: 'node',
                            style: {
                                'background-color': 'lightblue',
                                'width': '100px',
                                'height': '80px',
                                'shape': 'round-rectangle',
                                'label': 'data(label)'
                            }
                        },
                        {
                            selector: ':parent',
                            style: {
                                'background-color': 'lightgrey',
                                'shape': 'rectangle',
                                'label': 'data(label)',
                                'padding': '50px',
                            }
                        },
                        {
                            selector: 'edge',
                            style: {
                                'width': 4,
                                'line-color': 'lightgrey',
                                'target-arrow-color': 'lightgrey',
                                'target-arrow-shape': 'triangle',
                                'arrow-scale': 2,
                                'curve-style': 'bezier',
                            }
                        },
                        {
                            selector: '.eh-handle',
                            style: {
                                'background-color': 'violet',
                                'width': '30px',
                                'height': '30px'
                            }
                        },
                        {
                            "selector": ".center-center",
                            "style": {
                                "text-valign": "center",
                                "text-halign": "center"
                            }
                        },
                        {
                            "selector": ".test",
                            "style": {
                                "line-color": "red"
                            }
                        }
                    ]
                },
                elements: [],
                currentNodeIndex: 0
            }
        },
        methods: {

            /**
             * Moves through the workflow stage by stage, and picks a random choice for the ending 
             **/
            moveThroughWorkflow() {
                let currentNode = this.cy.nodes[this.currentNodeIndex];
                let nextEdge = currentNode.connectedEdges();
                nextEdge.addClass('test');
            },

            displayGraph() {

                this.elements = [ // flat array of nodes and edges
                        { // node n1
                            group: 'nodes', // 'nodes' for a node, 'edges' for an edge
                            // NB the group field can be automatically inferred for you but specifying it
                            // gives you nice debug messages if you mis-init elements


                            data: { // element data (put json serialisable dev data here)
                                id: 'savedNode', // mandatory (string) id for each element, assigned automatically on undefined
                                //parent: 'nparent', // indicates the compound node parent id; not defined => no parent
                                // (`parent` can be effectively changed by `eles.move()`)
                                label: "Saved"
                            },

                            // scratchpad data (usually temp or nonserialisable data)
                            scratch: {
                                _foo: 'bar' // app fields prefixed by underscore; extension fields unprefixed
                            },

                            position: { // the model position of the node (optional on init, mandatory after)
                                x: -100,
                                y: 0
                            },

                            selected: false, // whether the element is selected (default false)

                            selectable: true, // whether the selection state is mutable (default true)

                            locked: false, // when locked a node's position is immutable (default false)

                            grabbable: true, // whether the node can be grabbed and moved by the user

                            pannable: false, // whether dragging the node causes panning instead of grabbing

                            classes: ['foo', 'bar'], // an array (or a space separated string) of class names that the element has
                            classes: 'center-center'
                            // DO NOT USE THE `style` FIELD UNLESS ABSOLUTELY NECESSARY
                            // USE THE STYLESHEET INSTEAD
                            //style: { // style property overrides 
                            //    'background-color': 'red'
                            //}
                        },

                        { // node n2
                            data: { id: 'supervisorReviewNode', label: "In Supervisor's Review" },
                            renderedPosition: { x: 100, y: 0 }, // can alternatively specify position in rendered on-screen pixels
                            classes: 'center-center'
                        },

                        { // node n3
                            data: { id: 'chairReviewNode', label: "In Chair's Review" },
                            position: { x: 300, y: 0 },
                            classes: 'center-center'

                        },

                        { // 
                            data: { id: 'reviewCompletedNode', label: "Review Completed" },
                            position: { x: 500, y: 0 },
                            classes: 'center-center'

                        },

                        { // 
                            data: { id: 'adjudicationNode', label: "In Adjudication" },
                            position: { x: 700, y: 0 },
                            classes: 'center-center'

                        },

                        { // 
                            data: { id: 'acceptedNode', label: "Accepted" },
                            position: { x: 900, y: 0 },
                            classes: 'center-center'

                        },

                        { // 
                            data: { id: 'rejectedNode', label: "Rejected" },
                            position: { x: 900, y: -150 },
                            classes: 'center-center'

                        },

                        { // 
                            data: { id: 'deletedNode', label: "Deleted" },
                            position: { x: 900, y: 150 },
                            classes: 'center-center'

                        },

                        { // extra node
                            data: { id: 'extraN', parent: 'nparent', label: "Testing 123" },
                            position: { x: 0, y: -300 },
                            classes: 'center-center'

                        },

                        { // node nparent
                            data: { id: 'nparent', label: "Parent container" }
                        },

                        { // edge e1
                            data: {
                                id: 'e1',
                                // inferred as an edge because `source` and `target` are specified:
                                source: 'savedNode', // the source node id (edge comes from this node)
                                target: 'supervisorReviewNode'  // the target node id (edge goes to this node)
                                // (`source` and `target` can be effectively changed by `eles.move()`)
                            },

                            pannable: true // whether dragging on the edge causes panning
                        },
                        { // 
                            data: {
                                id: 'e2',
                                // inferred as an edge because `source` and `target` are specified:
                                source: 'supervisorReviewNode', // the source node id (edge comes from this node)
                                target: 'chairReviewNode'  // the target node id (edge goes to this node)
                                // (`source` and `target` can be effectively changed by `eles.move()`)
                            },

                            pannable: true // whether dragging on the edge causes panning
                        },
                        { // 
                            data: {
                                id: 'e3',
                                // inferred as an edge because `source` and `target` are specified:
                                source: 'chairReviewNode', // the source node id (edge comes from this node)
                                target: 'reviewCompletedNode'  // the target node id (edge goes to this node)
                                // (`source` and `target` can be effectively changed by `eles.move()`)
                            },

                            pannable: true // whether dragging on the edge causes panning
                        },
                        { // 
                            data: {
                                id: 'e4',
                                // inferred as an edge because `source` and `target` are specified:
                                source: 'reviewCompletedNode', // the source node id (edge comes from this node)
                                target: 'adjudicationNode'  // the target node id (edge goes to this node)
                                // (`source` and `target` can be effectively changed by `eles.move()`)
                            },

                            pannable: true // whether dragging on the edge causes panning
                        },
                        { // 
                            data: {
                                id: 'e5',
                                // inferred as an edge because `source` and `target` are specified:
                                source: 'adjudicationNode', // the source node id (edge comes from this node)
                                target: 'acceptedNode'  // the target node id (edge goes to this node)
                                // (`source` and `target` can be effectively changed by `eles.move()`)
                            },

                            pannable: true // whether dragging on the edge causes panning
                        },
                        { // 
                            data: {
                                id: 'e6',
                                // inferred as an edge because `source` and `target` are specified:
                                source: 'adjudicationNode', // the source node id (edge comes from this node)
                                target: 'rejectedNode'  // the target node id (edge goes to this node)
                                // (`source` and `target` can be effectively changed by `eles.move()`)
                            },

                            pannable: true // whether dragging on the edge causes panning
                        },
                        { // 
                            data: {
                                id: 'e7',
                                // inferred as an edge because `source` and `target` are specified:
                                source: 'adjudicationNode', // the source node id (edge comes from this node)
                                target: 'deletedNode'  // the target node id (edge goes to this node)
                                // (`source` and `target` can be effectively changed by `eles.move()`)
                            },

                            pannable: true // whether dragging on the edge causes panning
                        }
                    ];

                

                //this.cy.zoomingEnabled(false);

                //labels stuff from popperjs
                // create a basic popper on the first node
                //let popper1 = cy.nodes()[0].popper({
                //    content: () => {
                //        let div = document.createElement('div');

                //        div.innerHTML = 'Sticky Popper content1';

                //        document.body.appendChild(div);

                //        return div;
                //    },
                //    popper: {} // my popper options here
                //});

                //// create a basic popper on the core
                //let popper2 = this.cy.nodes()[1].popper({
                //    content: () => {
                //        let div = document.createElement('div');

                //        div.innerHTML = 'Sticky Popper content2';

                //        document.body.appendChild(div);

                //        return div;
                //    },
                //    //renderedPosition: () => ({ x: 100, y: 200 }), //this wont allow the stickied name to move though...
                //    popper: {} // my popper options here
                //});

                ////sticky label
                //let update = () => {
                //    popper1.update();
                //    popper2.update();
                //};

                //this.cy.nodes().each(node => {
                //    node.on('position', update);
                //});

                //this.cy.on('pan zoom resize', update);
            }
        },

        mounted(){
            this.displayGraph();
        }
    };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    #container{
        width:1000px;
        height:800px;
        border: solid black 1px;
    }
</style>

